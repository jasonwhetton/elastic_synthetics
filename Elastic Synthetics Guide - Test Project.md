# Elastic Synthetics Guide

## Prerequisites:

* Node >= 18.12.0
* Visual Studio Code

Creating a project

```console
$ npm init
```

Add synthetics to the project

```console
$ npm install @elastic/synthetics
```

Initialize synthetics to link it with your APM server:

```console
$ npx @elastic/synthetics init
```

You will be asked to provide the following information:

| Question | Answer |
| --- | ----------- |
| Do you use Elastic Cloud | *yes* |
| What is your cloud.id? | <cloud_id> |
| What is your API key? | <api_key> |
| Select the locations where you want to run monitors | *select the nearest geographically* |
| Set default schedule in minutes for all monitors | 10 |
| Choose project id to logically group monitors | *e.g. ellos* |
| Choose the target Kibana space | *default* |

## Running tests locally
In order to run the tests locally, use the following command:

```console
$ npx @elastic/synthetics .
```

You can filter the tests either by name or using tags:

### Filter tests ###

To filter using tags use the following syntax:

```console
$ npx @elastic/synthetics . --tags "guest"
```

![Filtering](/images/tags.png "Filter by tags")

*Note that tag filtering works on tags applied to a **journey**, not to the monitor.*

### Switching environments 

Elastic Synthetics respects the current node environment for configuration.

```console
$ NODE_ENV=local npx @elastic/synthetics . 
```

####### --params '{ "url": "http://localhost:8080" }'

In order to be able to run against different environments, you will probably want to keep the base URL of the site in the configuration.

![Monitor Params](/images/params.png "Setting parameters for a monitor")

You can then access the parameter in your journey steps as below.

![Monitor Configuration](/images/using_params.png "Passing parameters")

![Monitor Configuration](/images/tags.png "Passing parameters")

Note that the tags used to filter tests are applied to the journey, rather than the monitor.

To override the synthetics config for running locally, use the following syntax:

```console
$ npx @elastic/synthetics . --params '{ "url": "http://localhost:8080" }'
```

## Configuring the monitor

![Monitor Configuration](/images/monitor.png "Configuring a monitor")

| Field | Description |
| --- | ---- |
| id | Identifier for the monitor. Will be combined with project id and space. |
| schedule | How often the monitor will run. Must be one of the predefined values |
| enabled | Can be used to disable a monitor |
| locations | Defines which locations this monitor will be run from. Must be one of the predefined values |
| tags | Used for filtering in the Elastic APM UI |


## Deploying to APM server

You can deploy your tests to the APM server with the following command:

```console
$ npx @elastic/synthetics push --auth <api_key>
```
Elastic Synthetics will display a summary of the actions performed.

![Deploy](/images/push_ack.png "Push acknowledgement")

## CI/CD

You can run the tests as part of a CI/CD pipeline. Applying the reporter flag allows you to configure the appropriate output for the build server.

```console
$ npx @elastic/synthetics . --reporter [junit|default|json]
```
See the [documentation](https://www.elastic.co/guide/en/observability/8.6/synthetics-command-reference.html)

# Anatomy of a test

Each test consiste of a *journey* broken down into *steps*.

When writing end-to-end tests it's important to get the scope right. Unlike when writing Unit tests, we don't have the luxury of writing a large volume of tests. This is because running tests against a production environment is going to be orders of magnitude slower than running against a codebase with no external dependencies. Therefore, we have to increase the coverage of each test to compensate.

Ideally each journey will have an obvious start and end, but in practice it's not always clear. For example, if we're testing an e-commerce site and we want to test something on the product page, should we search for the product or navigate directly to the product?

The answer is we should try to include as much as possible, provided we maintain a *deterministic* test. That is to say, the result should be predictable and reliable.

It also means 
See the [documentation](https://www.elastic.co/guide/en/observability/8.6/synthetics-create-test.html)

# Recording using codegen

Whilst Elastic synthetics provides a recorder, it is currently in beta and may be preferable to use the standard playwright recorder.

You can start the recorder from the command line:

```console
$ npx playwright codegen
```

You may even pass a url to the codegen:

```console
$ npx playwright codegen www.example.com
```
You will need to copy/paste the code from the codegen tool into your journey and break down into appropriate steps.

## Best practices

### Use the recorder

Whilst it's tempting to structure your tests using reusable methods, it is not necessarily the best approach.

Using the recorder to produce your tests will provide the following benefits:

* Easy to recreate
* Easy to read (especially when using the getBy* methods)
* Stable

Creating dependency between tests by reusing code often leads to brittle tests, increasing the risk for false positives.

### Modify the source when necessary

If the codegen tool uses the locator method rather than getBy* it is usually an indication that you could improve the source in order to allow the codegen to make a better choice.

Ensure that images have appropriate text, for example.

### Separate the journey into logical steps

Using steps, it's possible to describe a journey in the same way you would to a person.

Prefer, for example:
```
journey: Add product to cart as user
    step: Go to the home page.
    step: Login to the site.
    step: Select a product.
    step: Add the product to the cart
```

to:

```
journey: Add product to the cart as user
    step: Add product to cart
```

Separating the steps will also allow performance metrics to show clearly the time taken for each step.

![Steps](/images/step_breakdown.png "Step duration")

## Avoid randomisation

Tests should be deterministic which means that they should accept the same input *every single time* and therefore the result should be predictable.

Introducing randomisation as a means to potentially expose more problems will lead to indeterministic tests, unpredictable results and therefore unstable tests.

## Avoid conditional logic

If you find yourself having to use conditional expressions such as *if* statements or *switch* then you are probably trying to achieve too much in a single test.

Considering separating into a single test for each condition.

# FAQ

Q: It seems that not all of the playwright assertions are available in Elastic synthetics.

A: *That's correct. We have raised it with Elastic and they have an open issue to resolve it.*

Q: I sometimes see an error with '/tmp/elastic-synthetics-unzip' yet I'm certain that my test should pass.

A: *This is an issue with Elastic synthetics and they have an open issue to resolve it.*

Q: My test is very unstable, I'm considering modifying my alert rules to tolerate more failures.

A: *Whilst you may, in some cases, be unable to avoid allowing for occasional failures due to network issues etc, it should always be a last resort. Look carefully at your test and try to determine if you you can structure it better to avoid the issue.*