# Clients

Right now the clients listed on this page are available.

> **Note**: If you want to contribute a client in an additional language please implement the [API spec](api.md#swagger-spec) and add it to this docs section!

## PHP

[Github.com](https://github.com/Scalify/puppet-master-client-php)

Install it via compose:

```bash
composer require scalify/puppet-master-client
```

Then create a client and use it as follows:

```php
<?php

use Scalify\PuppetMaster\Client\Client;
use Scalify\PuppetMaster\Client\ClientException;
use Scalify\PuppetMaster\Client\ClientInterface;
use Scalify\PuppetMaster\Client\CreateJob;
use Scalify\PuppetMaster\Client\Job;

require __DIR__ . '/vendor/autoload.php';

/**
 * @param $client
 */
function printCurrentJobs(ClientInterface $client)
{
    foreach (["created", "queued", "done"] as $status) {
        $jobs = $client->getJobsByStatus($status, 1, 1000);
        echo(sprintf("API currently has %s jobs at status %s", count($jobs), $status) . PHP_EOL);
    }

    $jobs = $client->getJobs(1, 1000);
    echo(sprintf("API currently has %s jobs at all", count($jobs)) . PHP_EOL);

    /** @var Job $job */
    foreach ($jobs as $job) {
        echo(sprintf("API has job %s at status %s", $job->getUUID(), $job->getStatus()) . PHP_EOL);
    }
}

function newTestCreateJob(): CreateJob
{
    $data = json_decode(file_get_contents("test-data/create-request.json"), true);

    return new CreateJob($data["code"], $data["vars"], []);
}

try {
    $client = new Client(new \GuzzleHttp\Client(), "http://localhost", "puppet");

    printCurrentJobs($client);

    $createdJob = $client->createJob(newTestCreateJob());
    echo(sprintf("Created Job %s at status %s", $createdJob->getUUID(), $createdJob->getStatus()) . PHP_EOL);

    do {
        $gotJob = $client->getJob($createdJob->getUUID());
        echo(sprintf("Got Job %s at status %s", $gotJob->getUUID(), $gotJob->getStatus()) . PHP_EOL);

        if ($gotJob->getStatus() !== Job::STATUS_DONE) {
            echo("Sleeping for 1 second ..." . PHP_EOL);
            sleep(1);
        }
    } while ($gotJob->getStatus() !== Job::STATUS_DONE);

    $client->deleteJob($createdJob->getUUID());
    echo(sprintf("Delete job %s", $createdJob->getUUID()) . PHP_EOL);
} catch (ClientException $e) {
    echo($e->getMessage() . PHP_EOL);
    exit(1);
}
```


## Golang

[Github.com](https://github.com/Scalify/puppet-master-client-go) | [godoc.org](https://godoc.org/github.com/Scalify/puppet-master-client-go)

Install it via go get:

    go get github.com/Scalify/puppet-master-client-go

Then crate a new client and use it as follows:

```go
package main

import "github.com/scalify/puppet-master-client-go"

func main() {
  client, err := puppet_master.NewClient("test", puppet_master.ApiV1Endpoint, "theapitokenigot")
	if err != nil {
		log.Fatalf("failed to create client: %v", err)
	}

	client.EnableDebugLogs()

	jobs, err := client.GetAllJobs(1, 100)
	if err != nil {
		log.Fatalf("failed to fetch jobs: %v", err)
	}

	log.Printf("Current page %d, last page %d, total %d", jobs.Meta.CurrentPage, jobs.Meta.LastPage, jobs.Meta.Total)

	for _, job := range jobs.Jobs {
		log.Printf("Job ID %v", job.UUID)
	}
}
```

More detailed docs on using the client can be found at [godoc.org](https://godoc.org/github.com/Scalify/puppet-master-client-go) or at the [README](https://github.com/Scalify/puppet-master-client-go/blob/master/README.md).
