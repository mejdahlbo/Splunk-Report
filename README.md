# Splunk - Report

## Forewords and Feedback
This write-up will reflect my thought process during the investigation and reasoning for actions taken during my **Home Lab - Splunk - Report**

Feel free to drop any feedback or point out anything I might’ve missed! I’m all about learning from others and my own mistakes. I really appreciate you taking the time to check out my project!

## Objective
We have been set the task to create a daily report named “**Daily - Windows Administrative Logins**” with a description of “**Daily report that reviews the successful administrative logins what occurred the day before.**”

## Data Requirements for our Report
-   The report should run every day at 0200 searching for “Yesterday” data, starting from the 07-01-2024
-   We are querying all Windows logins with administrative privileges
-   We make sure to filter out any SYSTEM and computer accounts
-   Our report should include the time, user & computer where they logged
-   Sorted by time where the earliest time is at the top

## Pre-requests
-   VM - _(WMware Workstation)_
-   Ubuntu Server - _(24.04.1 LTS)_
-   Splunk Enterprise _(9.3.1)_
-   Index file to use in Splunk _(5931 Events)_
-   Internet Browser

## Making sure we are using the correct index file for the lab
We are using the command **”index="mydfir-lab1”** to check we have **5931 events** as this will confirm we are working with the correct index file.

![1 - 5931 Events](https://github.com/user-attachments/assets/44d1f3a2-d8b4-4116-b9f5-c0d7386cf23f)

## Building our Query to list Logins with Administrative Privileges
We will reuse our query from our previous lab [Windows Event Dashboard](/1dcc03240996808fa158e16e2e4fe6ca?pvs=25) -
**index=mydfir-lab1 host=winvm EventCode=4720 user!=*$ user!=SYSTEM |stats count by user**

We adapt this one to so we get closer to our requirements as listed above, our new query looks like this -

**index=mydfir-lab1 host=winvm EventCode=4672 user!=*$ user!=SYSTEM |stats count by _time user ComputerName |sort -_time**

The field **EventCode** we change to **4672** because we want to monitor new Windows logons with administrative privileges.

![2 - 4672](https://github.com/user-attachments/assets/c1c4f4ec-a164-401c-8a31-ff162b0593f1)

Next we ensure that the date is set correctly by clicking on the date option.

![3 - Date Option](https://github.com/user-attachments/assets/5a5cfa72-17a4-402c-93ae-d368c3a6edd0)

Set the date from 07/01/2024 to 07/01/2024 and click Apply.

![4 - Setting Date](https://github.com/user-attachments/assets/dd3b9bc7-6c33-409e-98fd-5697d3694a91)

![5 - 22 Events](https://github.com/user-attachments/assets/336d301d-fb8e-47e5-9c48-1365ee623b01)

The image above shows our query and date set which gives us **22 events**.

## Account with Administrative Privileges
From our search we see under **Statistics** an account with administrative privileges which logged into the system, the account is **kporter**.

![6 - kporter](https://github.com/user-attachments/assets/6b8e041d-ea18-49f1-ab1c-1e0fc0017565)

If we look at the _time field we see the results sorted with the latest logins, we want to have the earliest logins listed first. We get this by changing the criteria from **|sort -_time** to **|sort +_time**

Our query now looks like this -

**index=mydfir-lab1 host=winvm EventCode=4672 user!=*$ user!=SYSTEM |stats count by _time user ComputerName |sort +_time**

![7 - Earliest](https://github.com/user-attachments/assets/8389f558-8fb7-41db-a167-db314d49eab4)

From the image above we now see the **_time** is sorted so we have the earliest entry first.

## Creating our Report
We create our report by clicking on **Save As** and then **Report**

![8 - Save As](https://github.com/user-attachments/assets/57eb2da8-efec-448b-8f29-24b21437f585)

Next we fill out our the fields matching our requirements, **Title - Daily - Windows - Successful Administrative Logins**, and in **Description - Daily report that reviews the successful administrative logins what occurred the day before**. We leave the Time Range Picker as No and click Save.

![9 - Save As](https://github.com/user-attachments/assets/79ea40d8-f188-4ac8-a3ca-272ffc21ab1b)

On the next page we click on **Schedule**

![10  - Schedule](https://github.com/user-attachments/assets/bf22cca0-4b6b-4d39-8659-867a69064fce)

We make sure we have the box **Schedule Report** checked so we have the time options available below.

![11 - Schedule Report](https://github.com/user-attachments/assets/73c07ec6-79a5-441b-ae9e-dc6f91c82f60)

From the drop down menu in **Schedule** we select **Run Every Day and At 2:00**

![12 - Yesterday](https://github.com/user-attachments/assets/2091bcff-e749-48ff-9642-afceac3d7ca8)

We want to search for yesterday’s data, we click on **Time Range** and from **Presets** we select Yesterday

![13 - Yesterday](https://github.com/user-attachments/assets/3d70475e-6d80-4bf6-b064-79c9e56e2baf)

We leave the **Schedule Priority, Schedule Window and Trigger Action** as is and click **Save**.

![14 - Yesterday](https://github.com/user-attachments/assets/de41eb37-8e3f-4ed5-9c89-ee4c96c98006)

We have now created a daily scheduled report what will look for any Windows Successful Administrative Logins. This way as a SOC Analyst we can take a look at the reports and perform auditing every day to see who is logging in that has admin privileges. Lastly we can ask, should this user have admin privileges and if not, then we can revoke the account permissions.

## Changelog
