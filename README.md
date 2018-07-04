# f8a-hpf-insights
**(fabric8-analytics-hpf-insights)**

HPF Matrix Factorizations for companion recommendation.

## Index:
* [Supported Ecosytems](#supported-ecosytems)
* [Build Upon](#build-upon)
* [Sample Request](#sample-request)
* [Sample Response](#sample-response)
* [Deploy Locally](#to-run-locally-via-docker-compose)
* [Deploy on DevCluster](#to-run-on-dev-cluster)
* [Run Unit Tests](#unit-tests)
* [Run Load Tests](#to-run-load-testing-for-recommendation-api)
* [Footnotes](#footnotes)
    * [Coding Standards](#coding-standards)
    * [Code Complexity Measurement](#code-complexity-measurement)
    * [Additional links](#additional-links)


## Supported ecosystems:
* Maven

## Build upon:
* https://github.com/arindamsarkar93/hcpf

## Sample Request
```
Endpoint: /api/v1/companion_recommendation
Method: POST
Content-type: application/json
Body:
```
```JSON
[
    {
        "ecosystem": "maven",
        "package_list": ["io.vertx:vertx-core"]

    }
]
```

## Sample Response
```
Content-type: application/json
```
```JSON
[
    {
        "companion_packages": [
            {
                "cooccurrence_probability": 37.5,
                "package_name": "org.apache.activemq:activemq-kahadb-store",
                "topic_list": []
            },
            {
                "cooccurrence_probability": 71.875,
                "package_name": "io.airlift:bootstrap",
                "topic_list": []
            }
        ],
        "ecosystem": "maven",
        "missing_packages": [],
        "package_to_topic_dict": {
            "io.vertx:vertx-core": []
        }
    }
]
```

## To run locally via docker-compose:

* Setup Minio and start Minio server so that `hpf-insights` is loaded as a folder inside it upon running. To use AWS S3 instead of Minio add your AWS S3 credentials in the next step instead of Miino credentials.
* Create a `.env` file and add credentials to it.
* In the `.env` set the `AWS_S3_ENDPOINT_URL` to `<blank>` for using AWS S3 and to `http://ip:port` for using Minio.
* `source .env`
* `docker-compose build`
* `docker-compose up`
* `curl  http://0.0.0.0:6006/` should return `status: ok`


## To run on dev-cluster:

* `cp secret.yaml.template secret.yaml`
* Add your AWS S3 credentials to `secret.yaml`
* `oc login`
* `oc new-project hpf-insights`
* `oc create -f secret.yaml`
* `oc process -f openshift/template.yaml -o yaml|oc create -f -` If you want to update the template.yaml and redeploy it, then do `oc process -f openshift/template.yaml -o yaml|oc apply -f -` Use apply instead of create for subsequent re-deployments.
* Go your Openshift console and expose the route
* `curl <route_URL>` should return `status:ok`

## Unit Tests
There's a script named `runtests.sh` that can be used to run all unit tests. The unit test coverage is reported as well by this script.

Usage:
```
./runtests.sh
```

## To run load testing for recommendation API:

* `pip install locustio==0.8.1`
* Bring up the service.
* `locust -f perf_tests/locust_tests.py --host=<URL of the service>`

### Footnotes:

#### Coding standards:

- You can use scripts `run-linter.sh` and `check-docstyle.sh` to check if the code follows [PEP 8](https://www.python.org/dev/peps/pep-0008/) and [PEP 257](https://www.python.org/dev/peps/pep-0257/) coding standards. These scripts can be run w/o any arguments:

```
./run-linter.sh
./check-docstyle.sh
```

The first script checks the indentation, line lengths, variable names, whitespace around operators etc. The second
script checks all documentation strings - its presense and format. Please fix any warnings and errors reported by these
scripts.

#### Code complexity measurement:

The scripts `measure-cyclomatic-complexity.sh` and `measure-maintainability-index.sh` are used to measure code complexity. These scripts can be run w/o any arguments:

```
./measure-cyclomatic-complexity.sh
./measure-maintainability-index.sh
```

The first script measures cyclomatic complexity of all Python sources found in the repository. Please see [this table](https://radon.readthedocs.io/en/latest/commandline.html#the-cc-command) for further explanation how to comprehend the results.

The second script measures maintainability index of all Python sources found in the repository. Please see [the following link](https://radon.readthedocs.io/en/latest/commandline.html#the-mi-command) with explanation of this measurement.

#### Additional links:
* [Pushing Image to Docker Hub](https://ropenscilabs.github.io/r-docker-tutorial/04-Dockerhub.html)
* [PAPER: Scalable Recommendation with Poisson Factorization](https://arxiv.org/abs/1311.1704)
* [PAPER: Hierarchical Compound Poisson Factorization](https://arxiv.org/abs/1604.03853)