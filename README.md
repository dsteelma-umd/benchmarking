# Benchmarking

## Introduction

To objectively assess the performance impact of Kubernetes configuration changes
on the application, an Apache JMeter-based Bash script has been provided for
automated benchmarking of the application.

More information about Apache JMeter is at <https://jmeter.apache.org/>.

## Prerequisites

The following assumes that Java 8 or later is already installed on the system.

1) Download the Apache JMeter 5.6.2 binary from
   <https://archive.apache.org/dist/jmeter/binaries/apache-jmeter-5.6.2.tgz>

    **Note:** The JMeter script does not appear to handle whitespace in filepaths,
    so it is best if JMeter is downloaded to a directory where the full path does
    not have any spaces.

2) Unpack the ".tgz" file:

    ```zsh
    $ tar -xvzf apache-jmeter-5.6.2.tgz
    ```

   This will create an "apache-jmeter-5.6.2" directory.

3) Add the "apache-jmeter-5.6.2/bin" directory to the PATH:

    ```zsh
    $ export PATH=$PATH:"`pwd`/apache-jmeter-5.6.2/bin"
    ```

## Benchmark Testing

The "benchmark" script has the following arguments:

```zsh
$ ./benchmark --website_host=<WEBSITE_HOST> --url_path=<URL_PATH> \
    --test_case=<TEST_CASE> --num_runs=<NUM_RUNS> \
    --num_users=<NUM_USERS> --num_seconds=<NUM_SECONDS> \
    --assert_string=<ASSERT_STRING> <RUN_NAME>
```

where:

* RUN_NAME: (Required) A name for the run, for reporting purposes.
* ASSERT_STRING: (Required) A string present in a successful response.
* WEBSITE_HOST: (Required) The hostname of the server to test.
* URL_PATH: The URL path to test. Defaults to "/".
* TEST_CASE: The test case to run (see test cases below). Defaults to
             "baseline.jmx".
* NUM_RUNS: The number of runs. Defaults to 1.
* NUM_USERS: The number of users in a run. Defaults to 10.
* NUM_SECONDS: The length of the test, in seconds. Defaults to 600 seconds.

### Examples

The following examples are run from the "benchmarking" project directory.

1) Run the "baseline.jmx" test one time against the "drum.sandbox.lib.umd.edu"
   server, using a run name of "ExampleBaselineRun1":

    ```zsh
    $ ./benchmark --website_host=drum.sandbox.lib.umd.edu \
          --assert_string="Welcome to the repository for University of Maryland research." \
          ExampleWebsiteStressTest1
    ```

2) Run the "constant_throughput.jmx" test twice against the
   "drum-test.lib.umd.edu" server with 5 users for 60 seconds, using a
   run name of "ExampleBaselineRun2":

    ```zsh
    $ ./benchmark --website_host=drum-test.lib.umd.edu \
        --assert_string="Welcome to the repository for University of Maryland research." \
        --test_case=constant_throughput.jmx \
        --num_runs=2 --num_users=5 --num_seconds=60 ExampleConstantThroughputRun2
    ```

## Test Cases

The following test cases are available.

### website_stress_test.jmx

Retrieves the given page for the website using the specified number of users.

### constant_throughput.jmx

Similar to the "website_stress_test.jmx" test, in that it retrieves the given
page for the website using the specific number of users. Adds the JMeter
"ConstantThroughputTimer" to pause threads as needed to maintain a target
throughput (85 samples per minute).

## Benchmark Reports

Benchmark reports are written in the "benchmarking" subdirectory in
subdirectories of the form `<DATE_STAMP>--<RUN_NAME>--<TEST_CASE>` where:

* DATE_STAMP: The date/time the benchmark was run
* RUN_NAME: The name of the run (from the "RUN_NAME" argument)
* TEST_CASE: The test case that was run.

For example: `2023-08-24_08-46-37--ExampleBaselineRun1--baseline.jmx`

All the report subdirectores are ignored by Git.

The report subdirectories contain the following:

* A "parameters_used.log" file containing the arguments for the benchmark.
* A "summary.txt" file summarizing the results of the benchmark.
* A subdirectory for each run (i.e., "run-1", "run-2", etc.), containing:
  * A "report" subdirectory with an Apache JMeter HTML report of the run
  * A "jmeter.log" file of the console output of the run
  * A "result.jtl" CSV file summarizing the result of each request to the
    server

## License

See the [LICENSE](LICENSE.md) file for license rights and limitations
(Apache 2.0).

