# Azurite V3

[![npm version](https://badge.fury.io/js/azurite.svg)](https://badge.fury.io/js/azurite)
[![Build Status](https://dev.azure.com/azure/Azurite/_apis/build/status/Azure.Azurite?branchName=master)](https://dev.azure.com/azure/Azurite/_build/latest?definitionId=20&branchName=master)

> Note:
> Azurite V2 has been moved to [legacy-master](https://github.com/Azure/azurite/tree/legacy-master) branch.
> Master branch has been updated with latest Azurite V3.
> V3 currently only supports Blob and Queue service, please use V2 for Table service for the time being.

| Version                                                            | Azure Storage API Version | Service Support       | Description                                       | Reference Links                                                                                                                                                                                                         |
| ------------------------------------------------------------------ | ------------------------- | --------------------- | ------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 3.7.0                                                              | 2019-07-07                | Blob<br>Queue         | Azurite V3 based on TypeScript & New Architecture | [NPM](https://www.npmjs.com/package/azurite) - [Docker](https://hub.docker.com/_/microsoft-azure-storage-azurite) - [Visual Studio Code Extension](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) |
| [Legacy (v2)](https://github.com/Azure/Azurite/tree/legacy-master) | 2016-05-31                | Blob, Queue and Table | Legacy Azurite V2                                 | [NPM](https://www.npmjs.com/package/azurite)                                                                                                                                                                            |

## Introduction

Azurite is an open source Azure Storage API compatible server (emulator). Based on Node.js, Azurite provides cross platform experiences for customers wanting to try Azure Storage easily in a local environment. Azurite simulates most of the commands supported by Azure Storage with minimal dependencies.

Azurite V2 is manually created with pure JavaScript, popular and active as an open source project. However, Azure Storage APIs are growing and keeping updating, manually keeping Azurite up to date is not efficient and prone to bugs. JavaScript also lacks strong type validation which prevents easy collaboration.

Compared to V2, Azurite V3 implements a new architecture leveraging code generated by a TypeScript Server Code Generator we created. The generator uses the same swagger (modified) used by the new Azure Storage SDKs. This reduces manual effort and more facilitates better code alignment with storage APIs.

3.0.0-preview is the first release version using Azurite's new architecture.

## Features & Key Changes in Azurite V3

- Blob storage features align with Azure Storage API version 2019-07-07 (Refer to support matrix section below)
  - SharedKey/Account SAS/Service SAS/Public Access Authentications
  - Get/Set Blob Service Properties
  - Create/List/Delete Containers
  - Create/Read/List/Update/Delete Block Blobs
  - Create/Read/List/Update/Delete Page Blobs
- Queue storage features align with Azure Storage API version 2019-07-07 (Refer to support matrix section below)
  - SharedKey/Account SAS/Service SAS
  - Get/Set Queue Service Properties
  - Preflight Request
  - Create/List/Delete Queues
  - Put/Get/Peek/Updata/Deleta/Clear Messages
- Features **NEW** on V3
  - Built with TypeScript and ECMA native promise and async features
  - New architecture based on TypeScript server generator. Leverage auto generated protocol layer, models, serializer, deserializer and handler interfaces from REST API swagger
  - Flexible structure and architecture, supports customizing handler layer implementation, persistency layer implementation, HTTP pipeline middleware injection
  - Detailed debugging log support, easy bug locating and reporting
  - Works with storage .Net SDK basic and advanced sample
  - SharedKey, AccountSAS, ServiceSAS, OAuth, Public Access authentication support
  - Keep updating with latest Azure Storage API version features (Refer to support matrix)

## Getting Started

Try with any of following ways to start an Azurite V3 instance.

### GitHub

After cloning source code, execute following commands to install and start Azurite V3.

```bash
npm ci
npm run build
npm install -g
azurite
```

### NPM

In order to run Azurite V3 you need Node.js >= 8.0 installed on your system. Azurite works cross-platform on Windows, Linux, and OS X.

After installation you can install Azurite simply with npm which is the Node.js package management tool included with every Node.js installation.

```cmd
npm install -g azurite
```

Simply start it with the following command:

```cmd
azurite -s -l c:\azurite -d c:\azurite\debug.log
```

or,

```cmd
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

This tells Azurite to store all data in a particular directory `c:\azurite`. If the `-l` option is omitted it will use the current working directory. You can also selectively start different storage services.

For example, to start blob service only:

```bash
$ azurite-blob -l path/to/azurite/workspace
```

Start queue service only:

```bash
$ azurite-queue -l path/to/azurite/workspace
```

### Visual Studio Code Extension

Azurite V3 can be installed from [Visual Studio Code extension market](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite).

You can quickly start or close Azurite by clicking Azurite **status bar item** or following commands.

Extension supports following Visual Studio Code commands:

- `Azurite: Start` Start all Azurite services
- `Azurite: Close` Close all Azurite services
- `Azurite: Clean` Reset all Azurite services persistency data
- `Azurite: Start Blob Service` Start blob service
- `Azurite: Close Blob Service` Close blob service
- `Azurite: Clean Blob Service` Clean blob service
- `Azurite: Start Queue Service` Start queue service
- `Azurite: Close Queue Service` Close queue service
- `Azurite: Clean Queue Service` Clean queue service

Following extension configurations are supported:

- `azurite.blobHost` Blob service listening endpoint, by default 127.0.0.1
- `azurite.blobPort` Blob service listening port, by default 10000
- `azurite.queueHost` Queue service listening endpoint, by default 127.0.0.1
- `azurite.queuePort` Queue service listening port, by default 10001
- `azurite.location` Workspace location path, by default existing Visual Studio Code opened folder
- `azurite.silent` Silent mode to disable access log in Visual Studio channel, by default false
- `azurite.debug` Output debug log into Azurite channel, by default false
- `azurite.loose` Enable loose mode which ignores unsupported headers and parameters, by default false
- `azurite.cert` Path to a PEM or PFX cert file. Required by HTTPS mode.
- `azurite.key` Path to a PEM key file. Required when `azurite.cert` points to a PEM file.
- `azurite.pwd` PFX cert password. Required when `azurite.cert` points to a PFX file.
- `azurite.oauth` OAuth oauthentication level. Candidate level values: `basic`.
- `azurite.skipApiVersionCheck` Skip the request API version check, by default false.

### [DockerHub](https://hub.docker.com/_/microsoft-azure-storage-azurite)

#### Run Azurite V3 docker image

```bash
docker run -p 10000:10000 -p 10001:10001 mcr.microsoft.com/azure-storage/azurite
```

`-p 10000:10000` will expose blob service's default listening port.
`-p 10001:10001` will expose queue service's default listening port.

Or just run blob service:

```bash
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite azurite-blob --blobHost 0.0.0.0
```

#### Run Azurite V3 docker image with customized persisted data location

```bash
docker run -p 10000:10000 -p 10001:10001 -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

`-v c:/azurite:/data` will use and map host path `c:/azurite` as Azurite's workspace location.

#### Customize all Azurite V3 supported parameters for docker image

```bash
docker run -p 8888:8888 -p 9999:9999 -v c:/azurite:/workspace mcr.microsoft.com/azure-storage/azurite azurite -l /workspace -d /workspace/debug.log --blobPort 8888 --blobHost 0.0.0.0 --queuePort 9999 --queueHost 0.0.0.0 --loose --skipApiVersionCheck
```

Above command will try to start Azurite image with configurations:

`-l //workspace` defines folder `/workspace` as Azurite's location path inside docker instance, while `/workspace` is mapped to `c:/azurite` in host environment by `-v c:/azurite:/workspace`

`-d //workspace/debug.log` enables debug log into `/workspace/debug.log` inside docker instance. `debug.log` will also mapped to `c:/azurite/debug.log` in host machine because of docker volume mapping.

`--blobPort 8888` makes Azurite blob service listen to port 8888, while `-p 8888:8888` redirects requests from host machine's port 8888 to docker instance.

`--blobHost 0.0.0.0` defines blob service listening endpoint to accept requests from host machine.

`--queuePort 9999` makes Azurite queue service listen to port 9999, while `-p 9999:9999` redirects requests from host machine's port 9999 to docker instance.

`--queueHost 0.0.0.0` defines queue service listening endpoint to accept requests from host machine.

`--loose` enables loose mode which ignore unsupported headers and parameters.

`--skipApiVersionCheck` skip the request API version check.

> In above sample, you need to use **double first forward slash** for location and debug path parameters to avoid a [known issue](https://stackoverflow.com/questions/48427366/docker-build-command-add-c-program-files-git-to-the-path-passed-as-build-argu) for Git on Windows.

> Will support more release channels for Azurite V3 in the future.

### NuGet

_Releasing Azurite V3 to NuGet is under investigation._

### Visual Studio

_Integrate Azurite with Visual Studio is under investigation._

## Supported Command Line Options

### Listening Host Configuration

Optional. By default, Azurite V3 will listen to 127.0.0.1 as a local server.
You can customize the listening address per your requirements.

#### Only Accept Requests in Local Machine

```cmd
--blobHost 127.0.0.1
--queueHost 127.0.0.1
```

#### Allow Accepting Requests from Remote (potentially unsafe)

```cmd
--blobHost 0.0.0.0
--queueHost 0.0.0.0
```

### Listening Port Configuration

Optional. By default, Azurite V3 will listen to 10000 as blob service port, and 10001 as queue service port.
You can customize the listening port per your requirements.

> Warning: After using a customized port, you need to update connection string or configurations correspondingly in your Storage Tools or SDKs.

#### Customize Blob/Queue Service Listening Port

```cmd
--blobPort 8888
--queuePort 9999
```

#### Let System Auto Select an Available Port

```cmd
--blobPort 0
--queuePort 0
```

> Note: The port in use is displayed on Azurite startup.

### Workspace Path Configuration

Optional. Azurite V3 needs to persist metadata and binary data to local disk during execution.

You can provide a customized path as the workspace location, or by default, Current process working directory will be used.

```cmd
-l c:\azurite
--location c:\azurite
```

### Access Log Configuration

Optional. By default Azurite will display access log in console. **Disable** it by:

```cmd
-s
--silent
```

### Debug Log Configuration

Optional. Debug log includes detailed information on every request and exception stack traces.  
Enable it by providing a valid local file path for the debug log destination.

```cmd
-d path/debug.log
--debug path/debug.log
```

### Loose Mode Configuration

Optional. By default Azurite will apply strict mode. Strict mode will block unsupported request headers or parameters. **Disable** it by enabling loose mode:

```cmd
-L
--loose
```

### Certificate Configuration (HTTPS)

Optional. By default Azurite will listen on HTTP protocol. Provide a PEM or PFX certificate file path to enable HTTPS mode:

```cmd
--cert path/server.pem
```

When `--cert` is provided for a PEM file, must provide coresponding `--key`.

```cmd
--key path/key.pem
```

When `--cert` is provided for a PFX file, must provide coresponding `--pwd`

```cmd
--pwd pfxpassword
```

### OAuth Configuration

Optional. By default, Azurite doesn't support OAuth and bearer token. Enable OAuth authentication for Azurite by:

```
--oauth basic
```

> Note. OAuth requires HTTPS endpoint. Make sure HTTPS is enabled by providing `--cert` parameter along with `--oauth` parameter.

Currently, Azurite supports following OAuth authentication levels:

#### Basic

In basic level, `--oauth basic`, Azurite will do basic authentication, like validating incoming bearer token, checking issuer, audience, expiry. But Azurite will NOT check token signature and permission.

### Skip API Version Check

Optional. By default Azurite will check the request API version is valid API version. Skip the API version check by:

```cmd
--skipApiVersionCheck
```

### Command Line Options Differences between Azurite V2

Azurite V3 supports SharedKey, Account Shared Access Signature (SAS), Service SAS, OAuth, and Public Container Access authentications, you can use any Azure Storage SDKs or tools like Storage Explorer to connect Azurite V3 with any authentication strategy.

An option to bypass authentication is **NOT** provided in Azurite V3.

## Supported Environment Variable Options

When starting Azurite from npm command line `azurite` or docker image, following environment variables are supported for advanced customization.

### Customized Storage Accounts & Keys

Azurite V3 allows customizing storage account names and keys by providing environment variable `AZURITE_ACCOUNTS` with format `account1:key1[:key2];account2:key1[:key2];...`.

For example, customize one storage account which has only one key:

```cmd
set AZURITE_ACCOUNTS="account1:key1"
```

Or customize multi storage accounts and each has 2 keys:

```cmd
set AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

Azurite will refresh customized account name and key from environment variable every minute by default. With this feature, we can dynamically rotate account key, or add new storage accounts on the air without restarting Azurite instance.

> Note. Default storage account `devstoreaccount1` will be disabled when providing customized storage accounts.

> Note. Should update connection string accordingly if using customized account name and key.

> Note. Use `export` keyword to set environment variable in Linux like environment, `set` in Windows.

### Customized Metadata Storage by External Database (Preview)

By default, Azurite leverages [loki](https://github.com/techfort/LokiJS) as metadata database.
However, as an in-memory database, loki limits Azurite's scalability and data persistency.
Set environment variable `AZURITE_DB=dialect://[username][:password][@]host:port/database` to make Azurite blob service switch to a SQL database based metadata storage, like MySql, SqlServer.

For example, connect to MySql or SqlServer by set environment variables:

```bash
set AZURITE_DB=mysql://username:password@localhost:3306/azurite_blob
set AZURITE_DB=mssql://username:password@localhost:1024/azurite_blob
```

When Azurite starts with above environment variable, it connects to the configured database, and creates tables if not exist.
This feature is in preview, when Azurite changes database table schema, you need to drop existing tables and let Azurite regenerate database tables.

> Note. Need to manually create database before starting Azurite instance.

> Note. Blob Copy & Page Blob are not supported by SQL based metadata implementation.

> Tips. Create database instance quickly with docker, for example `docker run --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:latest`. Grant external access and create database `azurite_blob` using `docker exec mysql mysql -u root -pmy-secret-pw -e "GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION; FLUSH PRIVILEGES; create database azurite_blob;"`. Notice that, above commands are examples, you need to carefully define the access permissions in your production environment.

## HTTPS Setup

Azurite natively supports HTTPS with self-signed certificates via the `--cert` and `--key`/`--pwd` options. You have two certificate type options: PEM or PFX. PEM certificates are split into "cert" and "key" files. A PFX certificate is a single file that can be assigned a password.

### PEM

#### Generate PEM Certificate and Key

You have a few options to generate PEM certificate and key files. We'll show you how to use [mkcert](https://github.com/FiloSottile/mkcert) and [OpenSSL](https://www.openssl.org/).

##### mkcert

[mkcert](https://github.com/FiloSottile/mkcert) is a utility that makes the entire self-signed certificate process much easier because it wraps a lot of the complex commands that you need to manually execute with other utilities.

###### Generate Certificate and Key with mkcert

1. Install mkcert: https://github.com/FiloSottile/mkcert#installation. We like to use choco `choco install mkcert`, but you can install with any mechanism you'd like.
2. Run the following commands to install the Root CA and generate a cert for Azurite.

```bash
mkcert -install
mkcert 127.0.0.1
```

That will create two files. A certificate file: `127.0.0.1.pem` and a key file: `127.0.0.1-key.pem`.

###### Start Azurite with HTTPS and PEM

Then you start Azurite with that cert and key.

```bash
azurite --cert 127.0.0.1.pem --key 127.0.0.1-key.pem
```

NOTE: If you are using the Azure SDKs, then you will also need to pass the `--oauth basic` option.

##### OpenSSL

[OpenSSL](https://www.openssl.org/) is a TLS/SSL toolkit. You can use it to generate certificates. It is more involved than mkcert, but has more options.

###### Install OpenSSL on Windows

1. Download and install the OpenSSL v1.1.1a+ EXE from http://slproweb.com/products/Win32OpenSSL.html
2. Set the following environment variables

```bash
set OPENSSL_CONF=c:\OpenSSL-Win32\bin\openssl.cfg
set Path=%PATH%;c:\OpenSSL-Win32\bin
```

###### Generate Certificate and Key

Execute the following command to generate a cert and key with [OpenSSL](https://www.openssl.org/).

```bash
openssl req -newkey rsa:2048 -x509 -nodes -keyout key.pem -new -out cert.pem -sha256 -days 365 -addext "subjectAltName=IP:127.0.0.1" -subj "/C=CO/ST=ST/L=LO/O=OR/OU=OU/CN=CN"
```

The `-subj` values are required, but do not have to be valid. The `subjectAltName` must contain the Azurite IP address.

###### Add Certificate to Trusted Root Store

You then need to add that certificate to the Trusted Root Certification Authorities. This is required to work with Azure SDKs and Storage Explorer.

Here's how to do that on Windows:

```bash
certutil –addstore -enterprise –f "Root" cert.pem
```

#### Start Azurite with HTTPS and PEM

Then you start Azurite with that cert and key.

```bash
Azurite --cert cert.pem --key key.pem
```

NOTE: If you are using the Azure SDKs, then you will also need to pass the `--oauth basic` option.

#### PFX

##### Generate PFX Certificate

You first need to generate a PFX file to use with Azurite.

You can use the following command to generate a PFX file with `dotnet dev-certs`, which is installed with the [.NET Core SDK](https://dotnet.microsoft.com/download).

```bash
dotnet dev-certs https --trust -ep cert.pfx -p <password>
```

> Storage Explorer does not currently work with certificates produced by `dotnet dev-certs`. While you can use them for Azurite and Azure SDKs, you won't be able to access the Azurite endpoints with Storage Explorer if you are using the certs created with dotnet dev-certs. We are tracking this issue on GitHub here: https://github.com/microsoft/AzureStorageExplorer/issues/2859

##### Start Azurite with HTTPS and PFX

Then you start Azurite with that cert and key.

```bash
azurite --cert cert.pem --key key.pem
```

NOTE: If you are using the Azure SDKs, then you will also need to pass the `--oauth basic` option.

##### Start Azurite

## Usage with Azure Storage SDKs or Tools

### Default Storage Account

Azurite V3 provides support for a default storage account as General Storage Account V2 and associated features.

- Account name: `devstoreaccount1`
- Account key: `Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

> Note. Besides SharedKey authentication, Azurite V3 supports account, OAuth, and service SAS authentication. Anonymous access is also available when container is set to allow public access.

### Customized Storage Accounts & Keys

As mentioned by above section. Azurite V3 allows customizing storage account names and keys by providing environment variable `AZURITE_ACCOUNTS` with format `account1:key1[:key2];account2:key1[:key2];...`.

For example, customize one storage account which has only one key:

```cmd
set AZURITE_ACCOUNTS="account1:key1"
```

Or customize multi storage accounts and each has 2 keys:

```cmd
set AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

### Connection Strings

#### HTTP Connection Strings

You can pass the following connection strings to the [Azure SDKs](https://aka.ms/azsdk) or tools (like Azure CLI 2.0 or Storage Explorer)

The full connection string is:

```bash
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Take blob service only, the full connection string is:

```bash
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
```

Or if the SDK or tools support following short connection string:

```bash
UseDevelopmentStorage=true;
```

#### HTTPS Connection Strings

The full HTTPS connection string is:

```bash
DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;
```

To use the Blob service only, the HTTPS connection string is:

```bash
DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;
```

If you used `dotnet dev-certs` to generate your self-signed certificate, then you need to use the following connection string, because that only generates a cert for `localhost`, not `127.0.0.1`.

```bash
DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://localhost:10000/devstoreaccount1;QueueEndpoint=https://localhost:10001/devstoreaccount1;
```

### Azure SDKs

To use Azurite with the [Azure SDKs](https://aka.ms/azsdk), you must use OAuth and HTTPs options:

`azurite --oauth basic --cert certname.pem --key certname-key.pem`

#### Azure Blob Storage

You can then instantiate BlobContainerClient, BlobServiceClient, or BlobClient.

```csharp
// With container url and DefaultAzureCredential
var client = new BlobContainerCLient(new Uri("https://127.0.0.1:10000/devstoreaccount1/container-name"), new DefaultAzureCredential());

// With connection string
var client = new BlobContainerClient("DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;", "container-name");

// With account name and key
var client = new BlobContainerClient(new Uri("https://127.0.0.1:10000/devstoreaccount1/container-name"), new StorageSharedKeyCredential("devstoreaccount1", "Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw=="));
```

#### Azure Queue Storage

You can also instantiate QueueClient or QueueServiceClient.

```csharp
// With queue url and DefaultAzureCredential
var client = new QueueCLient(new Uri("https://127.0.0.1:10001/devstoreaccount1/queue-name"), new DefaultAzureCredential());

// With connection string
var client = new QueueClient("DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;", "queue-name");

// With account name and key
var client = new QueueClient(new Uri("https://127.0.0.1:10001/devstoreaccount1/queue-name"), new StorageSharedKeyCredential("devstoreaccount1", "Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw=="));
```

### Storage Explorer

#### Storage Explorer with Azurite HTTP

Connect to Azurite by click "Add Account" icon, then select "Attach to a local emulator" and click "Connect".

#### Storage Explorer with Azurite HTTPS

By default Storage Explorer will not open an HTTPS endpoint that uses a self-signed certificate. If you are running Azurite with HTTPS, then you are likely using a self-signed certificate. Fortunately, Storage Explorer allows you to import SSL certificates via the Edit -> SSL Certificates -> Import Certificates dialog.

##### Import Certificate to Storage Explorer

1. Find the certificate on your local machine.
   - **OpenSSL**: You can find the PEM file at the location you created in the [HTTPS Setup](#https-setup) section above.
   - **mkcert**: You need to import the RootCA.pem file, which can be found by executing this command in the terminal: `mkcert -CAROOT`. For mkcert, you want to import the RootCA.pem file, not the certificate file you created.
   - **dotnet dev-certs**: Storage Explorer doesn't currently work with certs produced by `dotnet dev-certs`. We are tracking this issue on GitHub here: https://github.com/microsoft/AzureStorageExplorer/issues/2859
2. Open Storage Explorer -> Edit -> SSL Certificates -> Import Certificates and import your certificate.

If you do not set this, then you will get the following error:

```
unable to verify the first certificate
```

or

```
self signed certificate in chain
```

##### Add Azurite via HTTPS Connection String

Follow these steps to add Azurite HTTPS to Storage Explorer:

1. Right click on Local & Attached -> Storage Accounts and select "Connect to Azure Storage...".
2. Select "Use a connection string" and click Next.
3. Enter a name, i.e Azurite.
4. Enter the [HTTPS connection string](#https-connection-strings) from the previous section of this document and click Next.

You can now explore the Azurite HTTPS endpoints with Storage Explorer.

## Workspace Structure

Following files or folders may be created when initializing Azurite in selected workspace location.

- `azurite_db_blob.json` Metadata file used by Azurite blob service. (No when starting Azurite against external database)
- `azurite_db_blob_extent.json` Extent metadata file used by Azurite blob service. (No when starting Azurite against external database)
- `blobstorage` Persisted bindary data by Azurite blob service.
- `azurite_db_queue.json` Metadata file used by Azurite queue service. (No when starting Azurite against external database)
- `azurite_db_queue_extent.json` Extent metadata file used by Azurite queue service. (No when starting Azurite against external database)
- `queuestorage` Persisted bindary data by Azurite queue service.

> Note. Delete above files and folders and restart Azurite to clean up Azurite. It will remove all data stored in Azurite!!

## Differences between Azurite and Azure Storage

Because Azurite runs as a local instance for persistent data storage, there are differences in functionality between Azurite and an Azure storage account in the cloud.

### Storage Accounts

> Please reach to us or open issues if you need multi storage account support.

Azurite V3 supports a default account as General Storage Account V2 and provides features.

- Account name: `devstoreaccount1`
- Account key: `Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

### Endpoint & Connection URL

The service endpoints for Azurite are different from those of an Azure storage account. The difference is because the local computer does not perform domain name resolution, requiring Azurite endpoints to be local addresses.

When you address a resource in an Azure storage account, use the following scheme. The account name is part of the URI host name, and the resource being addressed is part of the URI path:

```
<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>
```

For example, the following URI is a valid address for a blob in an Azure storage account:

```
https://myaccount.blob.core.windows.net/mycontainer/myblob.txt
```

However, because the local computer does not perform domain name resolution, the account name is part of the URI path instead of the host name. Use the following URI format for a resource in Azurite:

```
http://<local-machine-address>:<port>/<account-name>/<resource-path>
```

For example, the following address might be used for accessing a blob in Azurite:

```
http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt
```

The service endpoints for Azurite blob service:

```
http://127.0.0.1:10000/<account-name>/<resource-path>
```

### Scalability & Performance

> Please reach to us if you have requirements or suggestions for a distributed Azurite implementation or higher performance.

Azurite is not a scalable storage service and does not support many concurrent clients. There is also no performance and TPS guarantee, they highly depend on the environments Azurite has deployed.

### Error Handling

> Please reach to us if you have requirements or suggestions for a specific error handling.

Azurite tries to align with Azure Storage error handling logic, and provides best-efforts alignment based on Azure Storage online documentation. But CANNOT provide 100% alignment, such as error messages (returned in error response body) maybe different (while error status code will align).

### API Version Compatible Strategy

Azurite V3 follows a **Try best to serve** compatible strategy with Azure Storage API versions:

- An Azurite V3 instance has a baseline Azure Storage API version.
  - A Swagger definition (OpenAPI doc) with the same API version will be used to generate protocol layer APIs and interfaces.
  - Azurite should implement all the possible features provided in this API service version.
- If an incoming request has **the same API version** Azurite provides, Azurite should handle the request with parity to Azure Storage.
- If an incoming request has a **higher API version** than Azurite, Azurite will return a InvalidHeaderValue error for `x-ms-version` (HTTP status code 400 - Bad Request).
- If an incoming request has a **lower API version** header than Azurite, Azurite will attempt to handle the request with Azurite's baseline API version behavior instead of that specified in the request.
- Azurite will return API version in response header as the baseline API version
- SAS accepts pattern from API version 2015-04-05

### RA-GRS

Azurite supports read-access geo-redundant replication (RA-GRS). For storage resources both in the cloud and in the local emulator, you can access the secondary location by appending -secondary to the account name. For example, the following address might be used for accessing a blob using the secondary in Azurite:

```
http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt
```

> Note. Secondary endpoint is not read-only in Azurite, which diffs from Azure Storage.

## Differences between Azurite V3 and Azurite V2

Both Azurite V3 and Azurite V2 aim to provide a convenient emulation for customers to quickly try out Azure Storage services locally. There are lots of differences between Azurite V3 and legacy Azurite V2.

### Architecture

Architecture in Azurite V3 has been refactored, it's more flexible and robust. It provides the flexibility to support following scenarios in the future:

- Use other HTTP frameworks instead of express.js
- Customized new handler layer implementation, such as redirecting requests to Azure Storage services
- Implement and inject a new persistency layer implementation, such as one based on a different database service
- Provide support for multiple azure storage accounts and authentication
- Detailed debug logging for easy issue investigation and request tracking
- Create HTTPS server
- ...

### Server Code Generator

Azurite V3 leverages a TypeScript server code generator based on Azure Storage REST API swagger specifications. This reduces manual efforts and ensures alignment with the API implementation.

### TypeScript

Azurite V3 selected TypeScript as its' programming language, as this facilitates broad collaboration, whilst also ensuring quality.

### Features Scope

Legacy Azurite V2 supports Azure Storage Blob, Queue and Table services.  
Azurite V3 currently only supports Azure Storage blob service. Queue service is supported after V3.2.0-preview.
Table service support is currently under discussion.

Azurite V3 supports features from Azure Storage API version 2019-07-07, and will maintain parity with the latest API versions, in a more frequent update frequency than legacy Azurite V2.

## TypeScript Server Code Generator

Azurite V3 leverages a TypeScript Node.js Server Code Generator to generate the majority of code from Azure Storage REST APIs swagger specification.  
Currently, the generator project is private, under development and only used by Azurite V3.
We have plans to make the TypeScript server generator public after Azurite V3 releases.  
All the generated code is kept in `generated` folder, including the generated middleware, request and response models.

## Support Matrix

Latest release targets **2019-07-07** API version **blob** service.  
Detailed support matrix:

- Supported Vertical Features
  - CORS and Preflight
  - SharedKey Authentication
  - OAuth authentication
  - Shared Access Signature Account Level
  - Shared Access Signature Service Level (Not support response header override in service SAS)
  - Container Public Access
- Supported REST APIs
  - List Containers
  - Set Service Properties
  - Get Service Properties
  - Get Stats
  - Get Account Information
  - Create Container
  - Get Container Properties
  - Get Container Metadata
  - Set Container Metadata
  - Get Container ACL
  - Set Container ACL
  - Delete Container
  - Lease Container
  - List Blobs
  - Put Blob (Create append blob is not supported)
  - Get Blob
  - Get Blob Properties
  - Set Blob Properties
  - Get Blob Metadata
  - Set Blob Metadata
  - Create Append Blob, Append Block
  - Lease Blob
  - Snapshot Blob
  - Copy Blob (Only supports copy within same account in Azurite)
  - Abort Copy Blob (Only supports copy within same account in Azurite)
  - Access control based on conditional headers
- Following features or REST APIs are NOT supported or limited supported in this release (will support more features per customers feedback in future releases)

  - SharedKey Lite
  - Static Website
  - Soft delete & Undelete Blob
  - Put Block from URL
  - Incremental Copy Blob

Latest version supports for **2019-07-07** API version **queue** service.
Detailed support matrix:

- Supported Vertical Features
  - SharedKey Authentication
  - Shared Access Signature Account Level
  - Shared Access Signature Service Level
  - OAuth authentication
- Supported REST APIs
  - List Queues
  - Set Service Properties
  - Get Service Properties
  - Get Stats
  - Preflight Queue Request
  - Create Queue
  - Get Queue Metadata
  - Set Queue Metadata
  - Get Queue ACL
  - Set Queue ACL
  - Delete Queue
  - Put Message
  - Get Messages
  - Peek Messages
  - Delete Message
  - Update Message
  - Clear Message
- Following features or REST APIs are NOT supported or limited supported in this release (will support more features per customers feedback in future releases)
  - SharedKey Lite

## License

This project is licensed under MIT.

## We Welcome Contributions!

> Go to [GitHub project](https://github.com/Azure/Azurite/projects) page or [GitHub issues](https://github.com/Azure/Azurite/issues) for the milestone and TODO items we are used for tracking upcoming features and bug fixes.

We are currently working on Azurite V3 to implement the remaining Azure Storage REST APIs.  
We finished the basic structure and majority of features in Blob Storage, as can be seen in the support matrix.  
The detailed work items are also tracked in GitHub repository projects and issues.

Any contribution and suggestions for Azurite V3 is welcome, please goto `CONTRIBUTION.md` for detailed contribution guidelines. Alternatively, you can open GitHub issues voting for any missing features in Azurite V3.

Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit <https://cla.microsoft.com.>

When you submit a pull request, a CLA-bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., label, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.
