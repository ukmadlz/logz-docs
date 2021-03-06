---
title: Ship Docker metrics
logo:
  logofile: docker.svg
  orientation: horizontal
data-source: Docker
open-source:
  - title: Docker Metrics Collector
    github-repo: docker-collector-metrics
contributors:
  - imnotashrimp
  - amosd92
shipping-tags:
  - container
---

To simplify shipping metrics from one or many sources,
we created Docker Metrics Collector.
Docker Metrics Collector is a container
that runs Metricbeat with the modules you enable at runtime.

#### Configuration

If you're not already running Docker Metrics Collector,
follow these steps.

Otherwise, stop the container, add
`docker`
to the `LOGZIO_MODULES` environment variable,
add `-v /var/run/docker.sock:/var/run/docker.sock:ro` to the command,
and restart.
You can find the `run` command and all parameters
in this procedure.

The `docker` module collects these metrics:
`container`,
`cpu`,
`diskio`,
`healthcheck`,
`info`,
`memory`,
`network`

<div class="tasklist">

##### Pull the Docker image

Download the Docker Metrics Collector image:

```shell
docker pull logzio/docker-collector-metrics
```

##### Run the container

You'll set your configuration using environment variables
in the `docker run` command.
Each parameter is formatted like this:
`--env ENV_VARIABLE_NAME="value"`.

For a complete list of options, see the parameters below the code block.👇

```shell
docker run --name docker-collector-metrics \
--env LOGZIO_TOKEN="<<SHIPPING-TOKEN>>" \
--env LOGZIO_MODULES="docker" \
-v /var/run/docker.sock:/var/run/docker.sock:ro \
logzio/docker-collector-metrics
```

###### Parameters for all modules

| Parameter | Description |
|---|---|
| LOGZIO_TOKEN <span class="required-param"></span> | Your Logz.io account token. {% include log-shipping/replace-vars.html token=true %} <!-- logzio-inject:account-token --> |
| LOGZIO_MODULES <span class="required-param"></span> | Comma-separated list of Metricbeat modules to be enabled on this container (formatted as `"module1,module2,module3"`). To use a custom module configuration file, mount its folder to `/logzio/logzio_modules`. |
| LOGZIO_REGION | Two-letter region code, or blank for US East (Northern Virginia). This determines your listener URL (where you're shipping the logs to) and API URL. <br> You can find your region code in the [Regions and URLs]({{site.baseurl}}/user-guide/accounts/account-region.html#regions-and-urls) table. |
| LOGZIO_TYPE <span class="default-param">`docker-collector-metrics`</span> | This field is needed only if you're shipping metrics to Kibana and you want to override the default value. <br> In Kibana, this is shown in the `type` field. Logz.io applies parsing based on `type`. |
| LOGZIO_LOG_LEVEL <span class="default-param">`"INFO"`</span> | The log level the module startup scripts will generate. |
| LOGZIO_EXTRA_DIMENSIONS | Semicolon-separated list of dimensions to be included with your metrics (formatted as `dimensionName1=value1;dimensionName2=value2`). <br> To use an environment variable as a value, format as `dimensionName=$ENV_VAR_NAME`. Environment variables must be the only value in the field. If an environment variable can't be resolved, the field is omitted. |
{:.paramlist}

###### Parameters for the Docker module

| Parameter | Description |
|---|---|
| DOCKER_MATCH_CONTAINER_NAME | Comma-separated list of containers you want to collect the metrics from. If a container's name partially matches a name on the list, that container's metrics are shipped. Otherwise, its metrics are ignored. <br> **Note**: Can't be used with `DOCKER_SKIP_CONTAINER_NAME`. |
| DOCKER_SKIP_CONTAINER_NAME | Comma-separated list of containers you want to ignore. If a container's name partially matches a name on the list, that container's metrics are ignored. Otherwise, its metrics are shipped. <br> **Note**: Can't be used with `DOCKER_MATCH_CONTAINER_NAME`. |
| DOCKER_PERIOD <span class="default-param">`10s`</span> | Sampling rate of metrics. The Docker API takes up to 2 seconds to respond, so we recommend setting this to `3s` or longer. |
| DOCKER_CERTIFICATE_AUTHORITY | Filepath to certificate authority for connecting to Docker over TLS. |
| DOCKER_CERTIFICATE | Filepath to CA certificate for connecting to Docker over TLS. |
| DOCKER_KEY | Filepath to Docker key for connecting to Docker over TLS. |
{:.paramlist}

##### Check Logz.io for your metrics

Give your metrics a few minutes to get from your system to ours,
and then open [Logz.io](https://app.logz.io/#/dashboard/kibana).

You can view your metrics on the
Docker overview
dashboard in Grafana.
Just click **<i class="fas fa-th-large"></i> > Manage** in the left menu,
then click
**Logz.io Dashboards >**
**Docker overview**.

</div>
