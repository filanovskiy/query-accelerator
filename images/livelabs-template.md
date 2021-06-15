# External Data Processing with ADW

*About this template: This file is used to demonstrate all of the features built into the LiveLabs conversion engine. Many of the features described here are optional and are so marked. Start with the [Use the LiveLabs Lab Markdown Template](https://confluence.oraclecorp.com/confluence/display/DBIDDP/Use+the+LiveLabs+Lab+Markdown+Template) Confluence page. Next, use the [LiveLabs Markdown Template What's New](https://confluence.oraclecorp.com/confluence/display/DBIDDP/LiveLabs+Markdown+Template+What%27s+New) page.*

## Introduction

*Describe the lab in one or two sentences, for example:* This lab walks you through the Steps to ...

*You may add an option video, using this format: [](youtube:YouTube video id)*

  [](youtube:zNKxJjkq0Pw)

Estimated Lab Time: n minutes

### Background
Enter background information here - this can include product information, or a technology overview, but also should cover what the workshop is about.

### Objectives

*List objectives for the lab - if this is the intro lab, list objectives for the workshop*

In this lab, you will:
* Objective 1
* Objective 2
* Objective 3
* [relative lab url test](?lab=need-help)

### Prerequisites

*Use this section to describe any prerequisites, including Oracle Cloud accounts, set up requirements, etc.*

* An Oracle Cloud Account - Please view this workshop's LiveLabs landing page to see which environments are supported
* Item no 2 with url - [URL Text](https://www.oracle.com).

*Note: If you have a **Free Trial** account, when your Free Trial expires your account will be converted to an **Always Free** account. You will not be able to conduct Free Tier workshops unless the Always Free environment is available. **[Click here for the Free Tier FAQ page.](https://www.oracle.com/cloud/free/faq.html)***

*This is the "fold" - below items are collapsed by default*

## **Step 1 (Optional)**: Create User
To run this Quick Start guide user can create dedicated account. It will help isolate it from other database objects and will simplify monitoring

## **Step 2**: Define external tables

<!-- Images -->

1. Explore Objects available in the public bucket
```
define uri_root = 'https://objectstorage.us-ashburn-1.oraclecloud.com/p/uwVf4H9xZ_k2ywUDrkW5u6mQJhHKJsrVAVI0Ly7VBQrzEWXP_ScLGuD-iLZXkF0Q/n/oraclebigdatadb/b/movieplex/o/workshop.db'
SELECT * FROM DBMS_CLOUD.LIST_OBJECTS(location_uri => '&uri_root/');
```
In a SQL Developer Web it will looks like:
    ![](images/list_of_the_files_oss.png)
2. Preview data
Data is stored in a plain file format and available thotugh the REST interface. User can run standard curl command from bash enviroment to preview data
```
$ curl https://objectstorage.us-ashburn-1.oraclecloud.com/p/uwVf4H9xZ_k2ywUDrkW5u6mQJhHKJsrVAVI0Ly7VBQrzEWXP_ScLGuD-iLZXkF0Q/n/oraclebigdatadb/b/movieplex/o/workshop.db/custsales/month=2019-01/custsales-2019-01.csv|head
```
Example of output:
    ![](images/curl.png)

3. Create set of the external tables, mapped to the public bucket
```
define uri_root = 'https://objectstorage.us-ashburn-1.oraclecloud.com/p/uwVf4H9xZ_k2ywUDrkW5u6mQJhHKJsrVAVI0Ly7VBQrzEWXP_ScLGuD-iLZXkF0Q/n/oraclebigdatadb/b/movieplex/o/workshop.db'
define csv_format = '{"dateformat":"YYYY-MM-DD", "skipheaders":"1", "delimiter":",", "ignoreblanklines":"true", "removequotes":"true", "blankasnull":"true", "trimspaces":"lrtrim", "truncatecol":"true", "ignoremissingcolumns":"true"}'
define json_format = '{"skipheaders":"0", "ignoreblanklines":"true"}'

BEGIN
     dbms_cloud.create_external_table(
        table_name => 'ext_customer_segment',
        file_uri_list => '&uri_root/customer_segment/customer_segment.csv',
        format => '&csv_format',
        column_list => 'segment_id number, name varchar2(100), short_name varchar2(100)'
        );   
    dbms_cloud.create_external_table(
        table_name => 'ext_genre',
        file_uri_list => '&uri_root/genre/genre.csv',
        format => '&csv_format',
        column_list => 'genre_id number, name varchar2(30)'
        );
     dbms_cloud.create_external_table(
        table_name => 'ext_movie',
        file_uri_list => '&uri_root/movie/movies.json',
        format => '&json_format',
        column_list => 'doc varchar2(30000)'
        );
     dbms_cloud.create_external_table(
        table_name => 'ext_custsales',
        file_uri_list => '&uri_root/custsales/*.csv',
        format => '&csv_format',
        column_list => 'day date, genre_id number, movie_id number, cust_id number, app varchar2(30), device varchar2(30), os varchar2(30), payment_method varchar2(30), list_price number, discount_type varchar2(30), discount_percent number,actual_price number'
        );  
    dbms_cloud.create_external_table(
        table_name => 'ext_customer',
        file_uri_list => '&uri_root/customer/*.csv',
        format => '&csv_format',
        column_list => 'CUST_ID	NUMBER,
                        LAST_NAME	VARCHAR2(200 BYTE),
                        FIRST_NAME	VARCHAR2(200 BYTE),
                        STREET_ADDRESS	VARCHAR2(400 BYTE),
                        POSTAL_CODE	VARCHAR2(10 BYTE),
                        CITY	VARCHAR2(100 BYTE),
                        STATE_PROVINCE	VARCHAR2(100 BYTE),
                        COUNTRY	VARCHAR2(400 BYTE),
                        COUNTRY_CODE	VARCHAR2(2 BYTE),
                        CONTINENT	VARCHAR2(400 BYTE),
                        AGE	NUMBER,
                        COMMUTE_DISTANCE	NUMBER,
                        CREDIT_BALANCE	NUMBER,
                        EDUCATION	VARCHAR2(40 BYTE),
                        EMAIL	VARCHAR2(500 BYTE),
                        FULL_TIME	VARCHAR2(40 BYTE),
                        GENDER	VARCHAR2(20 BYTE),
                        HOUSEHOLD_SIZE	NUMBER,
                        INCOME	NUMBER,
                        INCOME_LEVEL VARCHAR2(20 BYTE),
                        INSUFF_FUNDS_INCIDENTS	NUMBER,
                        JOB_TYPE	VARCHAR2(200 BYTE),
                        LATE_MORT_RENT_PMTS	NUMBER,
                        MARITAL_STATUS	VARCHAR2(8 BYTE),
                        MORTGAGE_AMT	NUMBER,
                        NUM_CARS	NUMBER,
                        NUM_MORTGAGES	NUMBER,
                        PET	VARCHAR2(40 BYTE),
                        PROMOTION_RESPONSE	NUMBER,
                        RENT_OWN	VARCHAR2(40 BYTE),
                        SEGMENT_ID	NUMBER,
                        WORK_EXPERIENCE	NUMBER,
                        YRS_CURRENT_EMPLOYER	NUMBER,
                        YRS_CUSTOMER	NUMBER,
                        YRS_RESIDENCE	NUMBER,
                        LOC_LAT	NUMBER,
                        LOC_LONG	NUMBER'
        );  
END;
/
```

4. Check JSON Data about movies

In a SQL Developer Web it will looks like:
    ![](images/json.png)

it's not a big table, put it into database and parse it:
```
create table movie as
select
    cast(m.doc.movie_id as number) as movie_id,
    cast(m.doc.title as varchar2(200 byte)) as title,    
    cast(m.doc.budget as number) as budget,
    cast(m.doc.gross as number) gross,
    cast(m.doc.list_price as number) as list_price,
    cast(m.doc.genre as varchar2(4000)) as genre,
    cast(m.doc.sku as varchar2(30 byte)) as sku,    
    cast(m.doc.year as number) as year,
    to_date(m.doc.opening_date, 'YYYY-MM-DD') as opening_date,
    cast(m.doc.views as number) as views,
    cast(m.doc.cast as varchar2(4000 byte)) as cast,
    cast(m.doc.crew as varchar2(4000 byte)) as crew,
    cast(m.doc.studio as varchar2(4000 byte)) as studio,
    cast(m.doc.main_subject as varchar2(4000 byte)) as main_subject,
    cast(m.doc.awards as varchar2(4000 byte)) as awards,
    cast(m.doc.nominations as varchar2(4000 byte)) as nominations,
    cast(m.doc.runtime as number) as runtime,
    substr(cast(m.doc.summary as varchar2(4000 byte)),1, 4000) as summary
from ext_movie m;
```

4. One example with bold **text**.

   If you add another paragraph, add 3 spaces before the beginning of the line to keep it in line with the numbered step.

5. Use tables sparingly:

 | Column 1 | Column 2 | Column 3 |
 | --- | --- | --- |
 | 1 | Some text or a link | More text  |
 | 2 |Some text or a link | More text |
 | 3 | Some text or a link | More text |

6. You can also include bulleted lists - make sure to indent 4 spaces:

    - List item 1
    - List item 2

7. Inline monospaced font is done with a single back ticks, for example `this is code`.

8. Code blocks are identified by three back ticks, indented to align with the step:

    ```
    This is a code block.
    ```

9. Long code block with line breaks test

    ```
    <copy>/* Specify the base URL that you copied from your files in OCI Object Storage in the define base_URL line below*/
    /* change idthydc0kinr to your real namespace. The name is case-sensitive. */
    /* change ADWCLab to your real bucket name. The name is case-sensitive. */
    /* change us-phoenix-1 to your real region name. The name is case-sensitive. */
    /* you can find these values on the OCI Console .. Storage .. Object Storage screen */
    set define on
    define base_URL='https://objectstorage.us-phoenix-1.oraclecloud.com/n/idthydc0kinr/b/ADWCLab/o'

    /* copy Channels table */
    begin
     dbms_cloud.copy_data(
        table_name =>'CHANNELS',
        credential_name =>'OBJ_STORE_CRED',
        file_uri_list =>'&base_URL/chan_v3.dat',
        format => json_object('ignoremissingcolumns' value 'true', 'removequotes' value 'true')
     );
    end;
    /

    /* copy Countries table */
    begin
     dbms_cloud.copy_data(
        table_name =>'COUNTRIES',
        credential_name =>'OBJ_STORE_CRED',
        file_uri_list =>'&base_URL/coun_v3.dat',
        format => json_object('ignoremissingcolumns' value 'true', 'removequotes' value 'true')
     );
    end;
    /

    /* Copy customers */
    begin
     dbms_cloud.copy_data(
        table_name =>'CUSTOMERS',
        credential_name =>'OBJ_STORE_CRED',
        file_uri_list =>'&base_URL/cust1v3.dat',
        format => json_object('ignoremissingcolumns' value 'true', 'removequotes' value 'true', 'dateformat' value 'YYYY-MM-DD-HH24-MI-SS')
     );
    end;
    /
    begin
     dbms_cloud.copy_data(
        table_name =>'SUPPLEMENTARY_DEMOGRAPHICS',
        credential_name =>'OBJ_STORE_CRED',
        file_uri_list =>'&base_URL/dem1v3.dat',
        format => json_object('ignoremissingcolumns' value 'true', 'removequotes' value 'true')
     );
    end;
    /
    begin
     dbms_cloud.copy_data(
        table_name =>'SALES',
        credential_name =>'OBJ_STORE_CRED',
        file_uri_list =>'&base_URL/dmsal_v3.dat',
        format => json_object('ignoremissingcolumns' value 'true', 'removequotes' value 'true', 'dateformat' value 'YYYY-MM-DD')
     );
    end;
    /
    begin
     dbms_cloud.copy_data(
        table_name =>'PRODUCTS',
        credential_name =>'OBJ_STORE_CRED',
        file_uri_list =>'&base_URL/prod1v3.dat',
        format => json_object('delimiter' value '|', 'quote' value '^', 'ignoremissingcolumns' value 'true', 'dateformat' value 'YYYY-MM-DD-HH24-MI-SS', 'blankasnull' value 'true')
     );
    end;
    /
    begin
     dbms_cloud.copy_data(
        table_name =>'PROMOTIONS',
        credential_name =>'OBJ_STORE_CRED',
        file_uri_list =>'&base_URL/prom1v3.dat',
        format => json_object('ignoremissingcolumns' value 'true', 'removequotes' value 'true', 'dateformat' value 'YYYY-MM-DD-HH24-MI-SS', 'blankasnull' value 'true')
     );
    end;
    /
    begin
     dbms_cloud.copy_data(
        table_name =>'SALES',
        credential_name =>'OBJ_STORE_CRED',
        file_uri_list =>'&base_URL/sale1v3.dat',
        format => json_object('ignoremissingcolumns' value 'true', 'removequotes' value 'true', 'dateformat' value 'YYYY-MM-DD', 'blankasnull' value 'true')
     );
    end;
    /
    begin
     dbms_cloud.copy_data(
        table_name =>'TIMES',
        credential_name =>'OBJ_STORE_CRED',
        file_uri_list =>'&base_URL/time_v3.dat',
        format => json_object('ignoremissingcolumns' value 'true', 'removequotes' value 'true', 'dateformat' value 'YYYY-MM-DD-HH24-MI-SS', 'blankasnull' value 'true')
     );
    end;
    /
    begin
     dbms_cloud.copy_data(
        table_name =>'COSTS',
        credential_name =>'OBJ_STORE_CRED',
        file_uri_list =>'&base_URL/costs.dat',
        format => json_object('ignoremissingcolumns' value 'true', 'dateformat' value 'YYYY-MM-DD', 'blankasnull' value 'true')
     );
    end;
    /</copy>
    ```

9. Use the copy function to allow your users to copy code snippets from LiveLabs into the clipboard.

    ```
  	<copy>Enclose the text you want to copy using the <copy> element.</copy>
    ```

10. Code snippets that include variables

  	```
    <copy>ssh -i <ssh-key-file></copy>
    ```

11. Syntax highlighting examples

    ```
    <copy>select * from employees;</copy>
    ```
    Sample with line breaks:

    ```
    <copy>class Simple{  

      public static void main(String args[]){  

        System.out.println("Hello Java");  

      }  

    }</copy>
    ```

    ```
    <copy>{  
        "employee": {  
            "name":       "sonoo",   
            "salary":      56000,   
            "married":    true  
        }  
    }</copy>
    ```

    ```
    <copy># This program prints Hello, world!

    print('Hello, world!')</copy>
    ```

12. You may want to turn highlighting off, to do so, use `nohighlight`:

    ```nohighlight
    <copy>class Simple{  
      public static void main(String args[]){  
        System.out.println("Hello Java");  
      }  
    }</copy>
    ```

## **Step 2:** Optional Advanced features

<!-- files -->

1. Files that you want the reader to download:

  When the file type is not recognized by the browser, you can use this format:

  Download the [starter SQL code](files/starter-file.sql).

  When the file type is recognized by the browser, it will attempt to render it. So you can use this format to force the download dialog box:

  Download the [sample JSON code](files/sample.json?download=1).

  *Note: do not include zip files, CSV, PDF, PSD, JAR, WAR, EAR, bin or exe files - you must have those objects stored somewhere else. We highly recommend using Oracle Cloud Object Store and creating a PAR URL instead. See [Using Pre-Authenticated Requests](https://docs.cloud.oracle.com/en-us/iaas/Content/Object/Tasks/usingpreauthenticatedrequests.htm)*

2. You can also include a file in a copy block, or offer it as a download in a single Step. For example, download the [starter SQL code](files/starter-file.sql), or copy it from the block below:

    ```
    <copy>[](include:starter-file.sql)</copy>
    ```

  To support this feature, you need to specify the location of the files you are including in the manifest as key-value pairs:

    ```
    "workshoptitle": "The Title of the Workshop is defined in the manifest",
    "include": {"starter-file.sql":"../../data-load/files/starter-file.sql",
                "sample.json":"../../data-load/files/sample.json"},
    "tutorials": [
    ```

3. You can also inject Markdown files into your lab. For example, the next substep is injected from a file called `injected-step.md`:

[](include:injected-step.md)

<!-- images -->

3. You can override the default image scaling by applying manual controls over image sizes:

  No image sizing applied: `![](images/pic2.png)`

  ![](images/pic2.png)

  50% of the width and use auto height: `![](images/pic2.png =50%x*)`

  ![](images/pic2.png =50%x*)

  absolute width and height (500 pixel by 200 pixels): `![](./images/pic2.png =500x200)`

  ![](./images/pic2.png =500x200)

  50% for both width and height:  `![](./images/pic2.png =50%x50%)`

  ![](./images/pic2.png =50%x50%)


4. Conditional content example (type="livelabs")

    Select your compartment. <if type="livelabs">If you are using a LiveLabs environment, be sure to select the compartment provided by the environment. Leave Always Free unchecked,</if><if type="alwaysfree">Choose any compartment, select "Always Free",</if> and enter `SecretPassw0rd` for the ADMIN password, then click **Create Autonomous Database**.

    ![](images/atp-settings-1.png)
    <if type="livelabs">![](images/atp-settings-2-notaf.png)</if>
    <if type="alwaysfree">![](images/atp-settings-2.png)</if>
    ![](images/atp-settings-3.png)


5. This is an example of a segment of the Markdown file injected before rendering:

[](include:injected-step.md)

## Learn More

*(optional - include links to docs, white papers, blogs, etc)*

* [URL text 1](http://docs.oracle.com)
* [URL text 2](http://docs.oracle.com)

## Acknowledgements
* **Author** - <Name, Title, Group>
* **Contributors** -  <Name, Group> -- optional
* **Last Updated By/Date** - <Name, Group, Month Year>
* **Workshop (or Lab) Expiry Date** - <Month Year> -- optional, use this when you are using a Pre-Authorized Request (PAR) URL to an object in Oracle Object Store.
