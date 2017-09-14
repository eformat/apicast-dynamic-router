# A "Self-Contained" Service Catalog for Apicast

## Introduction

This project provides a Service Catalog (list of services with their associated
environments and the matching URLs.

It is designed to be hosted on Apicast itself (or any nginx instance) in order
to simplify the deployment.

## How it works

API Services, Environments and their backends are declared in the `catalog.lua`.
The service catalog listens on port 8082.

You can query the Service Catalog by issuing an HTTP Request:
```
GET /catalog/services/{service_id}/environments/{environment}/target
```

For instance, to query the `prod` environment of service `123`:
```
curl -D - http://localhost:8082/catalog/services/123/environments/prod/target
```

## Development

First of all, setup your development environment as explained [here](https://github.com/3scale/apicast/tree/master#development--testing).

Then, issue the following commands:
```
git clone https://github.com/nmasse-itix/apicast-dynamic-router.git
git clone https://github.com/3scale/apicast.git
cd apicast
luarocks make apicast/*.rockspec --local
ln -s $PWD/../apicast-dynamic-router/catalog/catalog.conf apicast/sites.d/catalog.conf
ln -s $PWD/../apicast-dynamic-router/catalog/config.json config.json
mkdir -p custom
ln -s $PWD/../apicast-dynamic-router/catalog/catalog.lua custom/catalog.lua
```

Configure your apicast as explained [here](https://github.com/3scale/apicast/blob/master/doc/parameters.md)
and [here](https://github.com/3scale/apicast/blob/master/doc/configuration.md).
```
export THREESCALE_DEPLOYMENT_ENV=sandbox
export THREESCALE_CONFIG_FILE=config.json
export APICAST_LOG_LEVEL=debug
```

Finally, launch apicast:
```
bin/apicast -i 0 -m off
```

## Testing

The default catalog (`catalog.lua`) provides a few examples that you can test:
```
curl -D - http://localhost:8082/catalog/services/123/environments/prod/target
curl -D - http://localhost:8082/catalog/services/123/environments/dev/target
curl -D - http://localhost:8082/catalog/services/123/environments/bogus/target
curl -D - http://localhost:8082/catalog/services/456/environments/prod/target
curl -D - http://localhost:8082/catalog/services/456/environments/dev/target
curl -D - http://localhost:8082/catalog/services/456/environments/bogus_but_should_work/target
```
