---
title: Delete a project
slug: projects-delete-project
section: Projects
---

**Last updated 2nd June 2022**



## Objective  

To delete a project, you must be an organization owner or have the [manage plans permission](../administration/organizations.md#manage-your-organization-users)

To delete a Web PaaS project, including all data, code, and active environments:

> [!tabs]      
> In the console     
>> ``` false     
>> 
>> 
>> <!--This is in HTML to get the icon not to break the list. -->
>> <ol>
>>   <li>On the tile of the project you want to delete, click {{< icon more >}}</strong>More</strong>.</li>
>>   <li>Click <strong>Edit plan</strong>.</li>
>>   <li>Click <strong>Delete project</strong>.</li>
>>   <li>To confirm your choice, enter the project's name.</li>
>>   <li>Click <strong>Yes, Delete Project</strong>.</li>
>> </ol>
>> 
>> 
>> ```     
> Using the CLI     
>> ``` false     
>> 
>> 
>> 1. Run the following command:
>> 
>>    ```bash
>>    webpaas project:delete --project <PROJECT_ID>
>>    ```
>> 
>> 2. Read the consequences to deletion and enter `y`.
>> 3. Enter the project title to confirm.
>> 
>> ```     

You are billed only for the portion of a month when the project was active.
A user account with no projects has no charges.
