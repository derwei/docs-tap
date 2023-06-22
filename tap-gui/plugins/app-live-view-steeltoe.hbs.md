# Application Live View for Steeltoe Applications in Tanzu Application Platform GUI

This topic describes Application Live View pages for Steeltoe Applications in
Tanzu Application Platform GUI.

## <a id="details-page"></a> Details page

This is the default page loaded in the **Live View** section.
This page gives a tabular overview containing the following information:

- Application name
- Instance ID
- Location
- Actuator location
- Health endpoint
- Direct actuator access
- Framework
- Version
- New patch version
- New major version
- Build version

You can navigate between **Information Categories** by selecting from the drop-down menu on the
top right corner of the page.

![Details Page in UI](images/details-steeltoe.png)

## <a id="health-page"></a> Health page

To access the health page, select the **Health** option from the
**Information Category** drop-down menu.

The health page provides detailed information about the health of the application.
It lists all the components that make up the health of the application, such as readiness, liveness,
and disk space.
It displays the status and details associated with each of the components.

![Health Page in UI](images/health-steeltoe.png)

## <a id="environment-page"></a> Environment page

To access the **Environment** page, select the **Environment** option from the
**Information Category** drop-down menu.

The Environment page contains details of the applications' environment.
It contains properties including, but not limited to, system properties, environment variables, and
configuration properties (such as appsettings.json) in a Steeltoe application.

The page includes the following capabilities for `viewing` configured environment properties:

- The UI has search feature that enables the user to search for a property or values.
- Each property has a search icon at the right corner which helps the user quickly see all the occurrences of a specific property key without manually typing in the search field. Clicking the search button trims down the page to that property name.
- The **Refresh Scope** on the top right corner of the page probes the application to refresh all the environment properties.

The page also includes the following capabilities for `editing` configured environment properties:

- The UI allows the user to edit environment properties and see the live changes on the application. These edits are temporary and will go away if the underlying pod gets restarted.
- For each of the configured environment properties, the user can edit its value by clicking on **Override** button in the same row. After the value is saved, the user can view the message that the property has been overriden from the initial value. Also, the updated property is visible in the Applied Overrides section at the top of the page. The **Reset** button in the same row resets the environment property to the initial state. 
- The user can also edit or remove the overridden environment variables in the **Applied Overrides** section.
- The **Applied Overrides** section also enables the user to add new environment properties to the application.

> **Note:** The `management.endpoint.env.post.enabled=true` has to be set in the application config
> properties of the application, and a corresponding editable Environment has to be present in the
> application.

![Environment Page in UI](images/environment-steeltoe.png)

## <a id="log-levels-page"></a> Log Levels page

To go to the **Log Levels** page, select the **Log Levels** option from the **Information Category**
drop-down menu.
The **Log Levels** page provides access to the application’s loggers and the configuration of the
levels.

You can:

- Configure log levels, such as **INFO**, **DEBUG**, and **TRACE**, in real time from the UI
- Search for a package and edit its respective log level
- Configure the log levels at a specific class and package
- Deactivate all the log levels by changing the log level of root logger to **OFF**

Use the **Changes Only** toggle to display the changed log levels.
Use the search feature to search by logger name.
Click **Reset All** to reset all the loggers to the default state.

> **Note:** The UI allows the user to change the log levels and see the live changes on the application. These changes are temporary and will go away if the underlying pod gets restarted.

![Screenshot of the Log Levels page. The log level WARN is selected for every logger except Default.](images/log-levels-steeltoe.png)

## <a id="threads-page"></a> Threads page

To access the **Threads** page, select the **Threads** option from the
**Information Category** drop-down menu.

This page displays all details related to CLR threads and running processes of the application.
This tracks worker threads and completion port threads real-time.
Navigating to a thread state displays all the information about a particular thread and its stack
trace.

- The refresh icon refreshes to the latest state of the threads.
- To view more thread details, click the Thread ID.
- The page also has a feature to download thread dump for analysis.

![Threads Page in UI](images/threads-page-steeltoe-1.png)

![Threads Page in UI](images/threads-page-steeltoe-2.png)

## <a id="memory-page"></a> Memory page

To access the **Memory** page, select the **Memory** option from the **Information Category**
drop-down menu.

This page displays all details related to used and committed memory of the application.
This also displays the garbage collection count by generation (gen0/gen1).
The page also has a feature to download heap dump for analysis.

![Memory Page in UI](images/memory-page-steeltoe.png)

## <a id="metrics-page"></a> Metrics page

To access the **Metrics** page, select the **Metrics** option from the
**Information Category** drop-down menu.

The metrics page provides access to application metrics information.
You can choose from the list of various metrics available for the application, such as
`clr.memory.used`, `System.Runtime.gc-committed`, `clr.threadpool.active`, and so on.

After you choose the metric, you can view the associated tags.
You can choose the value of each of the tags based on filtering criteria.
Click **Add Metric** to add the metric to the page, which is refreshed every 5 seconds by default.

The UI on the Metrics page includes the features that allow you to:

- Pause the auto refresh feature by disabling the **Auto Refresh** toggle.
- Refresh the metrics manually by clicking **Refresh All**.
- Change the format of the metric value according to your needs.
- Delete a particular metric by clicking the minus symbol in the same row.

![Metrics Page in UI](images/metrics-steeltoe.png)

## <a id="actuator-page"></a> Actuator page

To access the **Actuator** page, select the **Actuator** option from the
**Information Category** drop-down menu.
The actuator page provides a tree view of the actuator data.
You can choose from a list of actuator endpoints and parse through the raw actuator data.

![Actuator Page in UI](images/actuator-steeltoe.png)

## <a id="troubleshooting"></a> Troubleshooting

You might run into cases where a workload running on your cluster does not show up in the
Application Live View overview, or the detail pages do not load any information while running, or
other similar issues.
For more information, see [Troubleshooting](../../app-live-view/troubleshooting.md).