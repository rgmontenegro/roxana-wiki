# MarkLogic Server

**Overview**:

- Admin Panel `http://localhost:8001`
- Application Builder `http://localhost:8002`
- Xquery Script Demos `http://localhost:8000/use-cases/`



## Concepts

MarkLogic Server manages databases which store XML documents or XML metadata. XQuery is the main language used for managing XML documents in MarkLogic. Binary data can be saved in MarkLogic, but file size is very limited.

### XML Documents

XML Documents are the atomic unit in the database.

### Forests

All the XML documents are stored in Forest which are logical groups. Each forest is directly related to the physical space the saved data occupies. A MarkLogic database can grow by adding more forests.

### Databases

MarkLogic databases are made of Forests. A database can store XML documents, binary data (images), and XQueries.

### App Servers

A MarkLogic database can be accessed by using a connection to an App Server. There are three connection types: HTTP Server, WebDAV, and XDBC (Java)

^ App Server ^ Description ^
| HTTP   | MarkLogic use an HTTP server as the main channel to serve content from a database directly to the end user. Marklogic will either serve HTML pages directly or process HTTP requests throughout a series of XQueries which will produce an HTML. This is possible since XQuery processes XML documents. |
| XDBC   | An XDBC App Server allows XCC clients to communicate with MarkLogic Databases. Java or .NET applications use XCC as the main medium to process or request XML documents from MarkLogic. IDEs such as the Oxygen XML Editor for Eclipse also use XCC to connect to MarkLogic. MarkLogic XCC client connectors can be downloaded from [[http://developer.marklogic.com/products|MarkLogic]]. |
| WebDAV | App Servers of this type allow reading and writing data (XML documents) into a MarkLogic database. WebDAV App Servers do not execute XQueries. Instead, they provide a straight an easy way to copy, delete, or update XML data located inside the database in an analogous way to managing files and folders in the Windows Explorer. |

* It is not recommended to use XQueries exclusively to mimic the behavior of programming languages such as PHP, ASP.NET or Java/JSP to serve dynamic content over the web. XQueries become easily complex and lack several features of programming languages.*

## W3C Use Case XQuery Example

MarkLogic includes a suite of XQuery script examples installed by default with the following details:

| Tables        | Are           | Cool  |
| ------------- |:-------------:| -----:|
| col 3 is      | right-aligned | $1600 |
| col 2 is      | centered      |   $12 |
| zebra stripes | are neat      |    $1 |


Database | Documents
--- | ---	
App Server           | Docs[HTTP] 
Application Folder   | `c:\Program Files\MarkLogic\Docs\`
XQuery Scripts       | `c:\Program Files\MarkLogic\Docs\use-cases\`

# MarkLogic Console

```bash
 
                                           # Start the server.
Programs > MarkLogic Server > Start MarkLogic Server (Right Click > Run as Admin)
                                           # Alternative. Start the server.
Control Panel > Services > Start MarkLogic Server (Right Click > Start)

 
```


# App Server Configuration

The configuration of the three types of App Servers is similar. The following is a description of the main options that are often customized:

## Server Name

Name of HTTP server

## Root
Specifies the location where the XQuery scripts to be processed by the App HTTP server are located. This should always end in "/" or "\".
\\
\\
The root can be located either in the File System or inside MarkLogic's database. MarkLogic will use the //**Modules**// option to interpret the //**Root**// location.

### File System

If the XQuery scripts are located outside of the root directory where MarkLogic was installed  an absolute path must be declared:
<code>c:\My Application\My Queries\</code>

When the XQuery scripts are located under the root directory of MarkLogic, a relative path can be used.

<code bash>My Queries/      # This will point to c:\Program Files\MarkLogic\My Queries\</code>


### Database Path

When using a database pathname it is possible to use a root directory:

<code bash>/       # Pointing to root of the database.</code>

The pathname can also be specific:

<code bash>My Queries/     # Pointing to /My Queries/</code>

### Port

The TCP port to be used by the App Server in order to serve content(XQueries, XML, etc) from the database. The TCP shouldn't be used by other services or MarkLogic App Servers.

### Modules

Defines whether the //**Root**// pathname will be interpreted as a location in the file system, specified by //**(file system)**//, or to a location inside the database, specified by a //**database name**//.
\\
\\
Modules is only available for HTTP and XDBC connections. WebDAV connections will always use the database name specified in the //**Database**// option as the location to be used by the option //**Root**//

### Database

Name of the database that the App Server is linked to.

### URL Rewriter

This option is available only in HTTP App Servers. It modifies how MarkLogic handles HTTP requests. The URL rewriter will use the XQuery script declared on this field. The location of this script follows the rules of the options //**Root**// and  //**Modules**//.

<code bash>/myScripts/urlRewrite.xqy       # URL rewriter script</code>

The following is an example of an URL Rewriter Script:

<code>
 

xquery version "1.0-ml";

let $url := xdmp:get-request-url()

let $url := fn:replace($url, "^/$", "/mainCode/")
let $url := fn:replace($url, "^/myWeb/([0-9a-zA-Z\.]+)$", "/subCode/$1")

return $url

 
</code>

For every request made to the HTTP App server to which this URL rewriter script belongs to, MarkLogic's function //xdmp:get-request-url// will defer the request to the appropriate handler specified by //fn:replace//. This change is made inside MarkLogic and it is not displayed to the end user. Examples according to the previous script are below:

^     Original Request                     ^  Internal Rewriten Request  ^
| http://www.mydomain.com/index.html       |  /mainCode/index.html       |
| http://www.mydomain.com/myWeb/index.html |  /subCode/index.html        |

# Log

MarkLogic tracks the most common script and server behavior in the logs located in:
<code>C:\Program Files\MarkLogic\Data\Logs</code>

  * HTTP related logs are named under the corresponding HTTP App Server port. Example: //8000_AccessLog.txt//
  * Most XQuery errors are logged under //ErrorLog.txt//

XQuery scripts can write in the log by using //**xdmp:log()**//

# Task Server

The task server specifies the basic environment under which //scheduled task// are performed.

MarkLogic will execute scheduled task according to the configuration specified in:
<code>Configure > Groups > Default > Task Server</code>

## Threads
Maximum number of threads that Marklogic will execute at the same time (concurrently)

## Max Time Limit
A query that last more than the seconds specified on this field will be forced to terminate.

# Scheduled Tasks

Scheduled tasks can be monitored, or cancelled in:
<code>Configure > Status > Show More(bottom)</code>
//MarkLogic's log can be used to track further details of a running scheduled script.//

Scheduled tasks are set in:
<code>Configure > Groups > Default > Scheduled Tasks</code>
//Scheduled tasks cannot be modified. Only deletion is available.//

## Task Path

The location of the XQuery script to be run, which is directly related to //**Task Root**//. For example: ///task.xqy// or ///mySubPath/task.xqy//

## Task Root

Specifies the base location were the script declared in //**Task Path**// resides. Additional modules required by the scheduled script will use this field as a base location/root to resolve modules location.

### Database
If the task root is located in the database then a root location can be declare as //**/**// while subdirectories can be declared as //**/myPath**//

### File System

If the location is on the file system a valid example is: //**c:\trunk\src**//

### Task Period

Unit of time that //**Task Type**// will use. For example if //**minutely**// is selected then a //**1**// on this field will make MarkLogic run the scheduled task every minute.

### Task Database

Database that the scheduled script will work in.

### Task Modules

Defines whether //**Task Root**// will be interpreted as a location in a database or as a location in the file system.

### Task User

A scheduled task is often run under the //**admin**// user, which is often written between parenthesis.

### Task Host

Hostname of the host were the task will be executed.

# Troubleshooting

  * Windows 7 is not officially supported until version 4.2-7. Hence, some issues may arise.
  * MarkLogic will use as much RAM as it can. Disabling it manually when it's not being used will make the system more stable.
  * Windows Firewall may block MarkLogic TCP ports. Enable them either by adding a rule for MarkLogic program, or individually to every MarkLogic TCP port (8000,8001,8002,etc)

# Reference

  * [[http://www.mail-archive.com/general@developer.marklogic.com/msg01883.html|Xdmp:default-permissions not working?]]
