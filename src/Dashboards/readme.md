# Introduction

This page includes dashboards that may be useful during a Graylog Evaluation. Dashboards are saved as [content packs](https://go2docs.graylog.org/5-2/what_more_can_graylog_do_for_me/content_packs.html) and can be imported using Graylog's content pack feature.

# What is a Graylog Content pack?

> Content packs are a convenient way to share configuration. A content pack is a JSON file which contains a set of configurations of Graylog components. This JSON file can be uploaded to Graylog instances and then installed. A user who takes the time to create an input, pipelines and a dashboard for a certain type of log format, can thus easily share their efforts with the community.
>
> -- https://go2docs.graylog.org/5-2/what_more_can_graylog_do_for_me/content_packs.html

# How to install a Graylog Content Pack

1. Using the top most menu, Navigate to **System** / **Content Packs**
2. Click the **Upload** button
    * ![Upload Button](img/content%20pack%20-%20upload%20button.png)
3. Either
    1. drag the content pack (`.json` ) file onto the **Choose File** button
    2. OR click the **Choose File** button, browse to, and select the content pack (`.json` ) file
    * ![Choose file Button](img/content%20pack%20-%20choose%20file%20button.png)
4. Click **Upload**
    * NOTE: at this point the content pack should be successfully uploaded, however, we still need to "activate" the content pack. This is called "installing" the content pack.
6. Type the name of the content pack into the **Filter** box. The filename of the content pack should match the name of the uploaded content pack.
    * ![filter input box](img/content%20pack%20-%20filter%20box.png)
7. You should see the content pack in the search result. Click **Install**
    * ![Install Button](img/content%20pack%20-%20install%20button.png)