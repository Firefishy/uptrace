# Uptrace Open Source demo

This example demonstrates how to quickly start Uptrace using Docker. To run Uptrace permanently, we
recommend using a DEB/RPM [package](https://get.uptrace.dev/guide/#packages) or a pre-compiled
[binary](https://get.uptrace.dev/guide/#binaries).

**Step 1**. Download the example using Git:

```shell
git clone https://github.com/uptrace/uptrace.git
cd example/docker
```

**Step 2**. Start the services using Docker:

```shell
docker-compose up -d
```

**Step 3**. Make sure Uptrace is running:

```shell
docker-compose logs uptrace
```

**Step 4**. Open Uptrace UI at http://localhost:14318

Uptrace will monitor itself using [uptrace-go](https://github.com/uptrace/uptrace-go) OpenTelemetry
distro. To get some test data, just reload the UI few times. It usually takes about 30 seconds for
the data to appear.

To configure OpenTelemetry for your programming language, see
[documentation](https://get.uptrace.dev/guide/).
