<!-- markdownlint-disable MD033 -->

# ![Dynatrace](res/Dynatrace_Logo.png) <br/> OneAgent Serverless plugin

dynatrace-oneagent is a plugin for [serverless framework](https://github.com/serverless/serverless) which will add Dynatrace monitoring automatically to serverless deployments.

## Disclaimer

This plugin is community contributed and **not officially supported by Dynatrace**.
In case of problems, feature requests, or questions [submit a ticket on GitHub](https://github.com/Dynatrace/serverless-oneagent/issues)

## Configuration

The Dynatrace OneAgent serverless plugin will configure serverless and serverless-webpack to bundle [Dynatrace npm module for PaaS](https://github.com/Dynatrace/agent-nodejs).

In regular serverless projects, the plugin will npm install and tailor [Dynatrace npm module for PaaS](https://github.com/Dynatrace/agent-nodejs) during serverless packaging process. It will also reconfigure the function handler definition to load Dynatrace OneAgent at function instance startup.

In serverless-webpack case, the plugin will configure webpack to include [Dynatrace npm module for PaaS](https://github.com/Dynatrace/agent-nodejs) into the bundle. The resulting deployment zip file will contain the Javascript file for the handler function and a node_modules folder with Dynatrace OneAgent.

Enabling your serverless project for Dynatrace OneAgent is a two steps process:

1. add plugin to `serverless.yml`
2. specify OneAgent options

### Add plugin to `serverless.yml`

Extend the `plugins` list of the projects `serverless.yml` file with `@dynatrace/serverless-oneagent` plugin.

```yaml {.line-numbers}
service: dynatrace-oneagent-sample

plugins:
  - '@dynatrace/serverless-oneagent'

provider:
  name: aws
  runtime: nodejs10.x

functions:
  hello:
    handler: index.hello
    events:
      - http:
          method: GET
          path: hello
```

### Specify OneAgent options

OneAgent options can be specified in `serverless.yml` file or serverless (sls) command line.

> The option string can be obtained from serverless configuration screen (Deploy Dynatrace > Setup Serverless integration).

Add following to `serverless.yml`:

```yaml
custom:
  serverless-oneagent:
    options: '{"server":"...","tenant":"...", "tenanttoken":"..."}'
```

If you do not want to add OneAgent options to the `serverless.yml` , the options can be specified as a command line argument to serverless (sls) command.

```shell
serverless deploy --dt-oneagent-options='{"dynatraceTagPropertyPath":"headers.x-dynatrace","server":"...","tenant":"...","tenanttoken":"..."}'
```

### Options summary

| `serverless.yml`| command line | description |
| ---| ---| --- |
| options | --dt-oneagent-options=\<option string\> | Specifies OneAgent options |
| npmModuleVersion | --dt-oneagent-module-version=\<version\> | Specifies the version of OneAgent for PaaS module. Default is `latest`, specify `next` for @next version.|
| verbose | --verbose | enables extended output of plugin processing. --verbose enables verbose mode for all plugins, while verbose option in `serverless.yml` enables verbose output for this plugin only.|
| exclude | --dt-exclude | exclude given list of functions from instrumentation. Separate function names with comma on command line.
| skipUninstall  | --dt-skip-uninstall | de-installation of @dynatrace/oneagent module after packaging might interfere with alternative dependency management tools like [lerna](https://github.com/lerna/lerna). This option suppresses the de-installation of @dynatrace/oneagent npm module. |

```yaml
...
functions:
  lambdaTest1:
    handler: test/first.handler

  lambdaTest2:
    handler: test/second.handler
...
custom:
  serverless-oneagent:
    # enable serverless-oneagent plugin verbose mode
    verbose: true
    # specify @next Dynatrace OneAgent npm module
    npmModuleVersion: next
    exclude:
      - lambdaTest2
```

```shell
serverless deploy --dt-oneagent-module-version=next --dt-oneagent-options='{"dynatraceTagPropertyPath":"headers.x-dynatrace","server":"...","tenant":"...","tenanttoken":"..."}' --verbose --dt-exclude=lambdaTest2
```

### Yarn configuration

Serverless plugin installs automatically `@dynatrace/oneagent` dependency at build time which causes a Yarn build error. To work around that build error, manually add `@dynatrace/oneagent` in the `package.json` file

e.g.

```yaml
"dependencies": {
        "@dynatrace/oneagent": "latest"
},
"devDependencies": {
        "@dynatrace/serverless-oneagent": "xxx"
}
 ```

## Samples

The samples folder contains ready to go serverless projects.

+ AWS Lambda with Node.js runtime [serverless](samples/aws-lambda-node.js/README.md)
+ AWS Lambda with Node.js runtime and serverless-webpack [serverless](samples/aws-lambda-node.js-webpack/README.md)

## Supported provider and runtime environments

+ The current plugin version supports following deployments
  + AWS Lambda with Node.js runtime version 8.x, 10.x and 12.x

## Support

In case of problems, feature requests, or questions submit a [ticket](https://github.com/Dynatrace/serverless-oneagent/issues).
