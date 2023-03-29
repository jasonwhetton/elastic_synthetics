# Elastic Synthetics Guide - User Interface

## Viewing the monitors

### Opening the monitor page

You can nagivate to the monitors page either by navigating to *Observability -> Monitors* or by using the shortcut *Uptime* in the search bar.

![Uptime](/images/navigate_to_monitors.png "Navigate to uptime")

---

### Filtering

Monitor results will be shown for the period selected in the range picker. You can set the page to auto refresh.

You can also filter by ID, name, URL, port or tags

![Uptime](/images/monitor_filter_period.png "Filter period")

---

### Test Detail

Clicking on the link for the test allows you to see a detailed view of the test results.

![Uptime](/images/test_detail.png "Test detail")

---

### Test Run

In the History pane you can see previous tests during the specified date range with results and the total duration. If you click on a specific test run you can see a a breakdown of the steps.

![Uptime](/images/test_run.png "Test run")

---
### Monitor Management

The filtered view will only show tests that were registered during the date range applied by the filter. If you wish to see all currently registered tests, click on the *Monitor Management* link.

Project monitors can only be viewed here, not edited.

![Uptime](/images/monitor_management.png "Monitor Management")

---
### Creating Alerts

You can create alerts that will send a notification when one or more of your monitors is failing.

| Field | Description |
| --- | --- |
| Name | A name for the alert |
| Tags | Tags for filtering in the alert list |
| Check every | How often the alert will check the monitor status |
| Notify | 'On status changes' is the default and will result in alerts *only* when the alert is first raised. |
| Filter | Determines which monitors will be included in the alert |
| Status check | Receive alert based on downtime |
| Availability | Receive alerts based on availability |

When deciding the frequency for alert checks you need to consider the period for each of the tests to be included in the alert.

Consider the following example:

![Alerts](/images/status_check.png "Status Check")

If the period of any test is greater than 3 minutes, this alert will *never* apply to it.

See the [documentation](https://www.elastic.co/guide/en/observability/current/monitor-status-alert.html) for more detail.

![Alerts](/images/alert_create.png "Alert Creation")



![Alerts](/images/alert_connectors.png "Alert Connectors")

---
### Connectors

Add a new connector by navigating to the shortcut page.

![Connectors](/images/connector_shortcut.png "Connectors")

---

Manage the list of connectors here. Example connectors are email, webhook.

![Connectors](/images/connector_view.png "Connectors")