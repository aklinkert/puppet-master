# Clients

Right now the clients listed on this page are available. If you want to contribute a client in an additional language

## PHP

[Github.com](https://github.com/Scalify/puppet-master-client-php)


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
