package main

import (
	"fmt"
	"net/url"
	"os"
	"time"

	"github.com/tebeka/flags"
)

var config struct {
	in      *os.File
	name    string
	port    int
	start   time.Time
	retries int
	url     *url.URL
}

func main() {
	flags.Add.File(&config.in, 'r', "input", "input file")
	flags.Add.Int(&config.retries, checkRetries, "retries", "number of retries")
	flags.Add.Port(&config.port, "port", "port to listen on")
	flags.Add.String(&config.name, checkName, "name", "logger name")
	flags.Add.Time(&config.start, time.RFC3339, "start", "start time")
	flags.Add.URL(config.url, "url", "url to hit")
	flags.Parse()

	fmt.Printf("in: %q\n", config.in.Name())
	fmt.Printf("name: %q\n", config.name)
	fmt.Printf("port: %d\n", config.port)
	fmt.Printf("retries: %d\n", config.retries)
	fmt.Printf("start: %s\n", config.start)
	fmt.Printf("url: %q\n", config.url.String())

	// Output:
	// in: "/dev/null"
	// name: "lassie"
	// port: 999
	// retries: 3
	// start: 2019-11-26 19:23:42 +0000 UTC
	// url: "http://example.com"
}

func checkRetries(n int) error {
	if n < 0 || n > 10 {
		return fmt.Errorf("retries = %d not in range [0:10]", n)
	}
	return nil
}

func checkName(s string) error {
	if len(s) == 0 {
		return fmt.Errorf("empty name")
	}
	return nil
}

func init() {
	// Set defaults
	config.in = os.Stdin
	config.name = "bugs"
	config.port = 8080
	config.retries = 1
	config.start = time.Now()
	config.url, _ = url.Parse("http://localhost:8080")
}
