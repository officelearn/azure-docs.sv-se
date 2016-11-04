---
title: Använd Data Lake Store Java SDK för att utveckla program | Microsoft Docs
description: Använd Azure Data Lake Store Java SDK för att utveckla program
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: paulettm
editor: cgronlun

ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/18/2016
ms.author: nitinme

---
# Kom igång med Azure Data Lake Store med hjälp av Java
> [!div class="op_single_selector"]
> * [Portalen](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> 
> 

Lär dig mer om att använda Azure Data Lake Store Java SDK för att skapa ett Azure Data Lake-konto och utföra grundläggande åtgärder som att skapa mappar, ladda upp och hämta filer, ta bort ditt konto, osv. Mer information om Data Lake finns i [Azure Data Lake Store](data-lake-store-overview.md).

## Azure Data Lake Store Java SDK
Följande länkar ger hämtningsplatsen för Java SDK för Data Lake Store och Java SDK-referensen. För de här självstudierna behöver du inte ladda ner SDK eller följa referensdokumentet. Dessa länkar är endast till som information.

* Källkoden för Java SDK för Data Lake Store är tillgänglig på [GitHub](https://github.com/Azure/azure-sdk-for-java).
* Referens för Java SDK för Data Lake Store är tillgänglig på [https://azure.github.io/azure-sdk-for-java/](https://azure.github.io/azure-sdk-for-java/).

## Krav
* Java Development Kit (JDK) 8 (med Java version 1.8).
* IntelliJ eller en annan lämplig Java Development Environment. Det här är valfritt men rekommenderas. Instruktionerna nedan använder IntelliJ
* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Aktivera din Azure-prenumeration** för en förhandsversion av Data Lake Store. Se [instruktioner](data-lake-store-get-started-portal.md#signup).
* **Skapa ett program i Azure Active Directory**. Det finns två sätt att autentisera med Azure Active Directory – **interaktivt** och **icke-interaktivt**. Det finns olika förutsättningar baserat på hur du vill autentisera.
  
  * **För interaktiv autentisering** – Du måste skapa ett **Native Client-program** i Azure Active Directory. När du har skapat programmet, hämtar du följande värden som är relaterade till programmet.
    
    * Hämta **klient-ID** och **omdirigerings-URI** för programmet
    * Ange delegerade behörigheter
  * **För icke-interaktiv autentisering** (används i den här artikeln) – Du måste skapa en **Webbapp** i Azure Active Directory. När du har skapat programmet, hämtar du följande värden som är relaterade till programmet.
    
    * Hämta **klient-ID**, **klienthemlighet** och **omdirigerings-URI** för programmet
    * Ange delegerade behörigheter
    * Tilldela Azure Active Directory-programmet till en roll. Rollen kan vara för den nivå av omfång för vilken du vill ge behörighet till Azure Active Directory-programmet. Du kan till exempel tilldela programmet på prenumerationsnivån eller på en resursgruppsnivå.
    
    Se [Skapa Active Directory-program och tjänstens huvudnamn med hjälp av portalen](../resource-group-create-service-principal-portal.md) för anvisningar om hur du hämtar dessa värden, anger behörigheter och tilldelar roller.

## Hur autentiserar jag med Azure Active Directory?
Kodfragmentet nedan innehåller koden för **icke-interaktiv** autentisering, där programmet tillhandahåller egna autentiseringsuppgifter.

Du måste ge ditt program behörighet att skapa resurser i Azure för att dessa självstudier ska fungera. Det **rekommenderas starkt** att du endast ger det här programmet deltagarbehörighet till en ny, oanvänd och tom resursgrupp i din Azure-prenumeration för självstudierna.

## Skapa ett Java-program
1. Öppna IntelliJ och skapa ett nytt Java-projekt med mallen **Kommandoradsapp**. Slutför guiden för att skapa projektet.
2. Öppna **Fil** -> **Projektstruktur** -> **Moduler** (under Projektinställningar) -> **Beroenden** -> **+** -> **Bibliotek** -> **Från Maven**.
3. Sök efter följande Maven-paket och lägg till dem till ditt projekt:
   
   * com.microsoft.azure:azure-mgmt-datalake-store:1.0.0-beta1.2
   * com.microsoft.azure:azure-mgmt-datalake-store-uploader:1.0.0-beta1.2
   * com.microsoft.azure:azure-client-authentication:1.0.0-beta2
4. I den vänstra rutan, gå till **src**, **huvudsakliga**, **java**, **\<paketnamn>** och öppna sedan **Main.java** och ersätt det befintliga kodblocket med följande kod. Ange dessutom värdena för parametrarna som påpekas i kodfragmentet, som **localFolderPath**, **DATA-LAKE-STORE-NAME**, **RESOURCE-GROUP-NAME** och ersätt platshållarna för **CLIENT-ID**, **CLIENT-SECRET**, **TENANT-ID** och **SUBSCRIPTION-ID** med information om din prenumeration och dess Azure Active Directory. Information om hur du hittar den här informationen finns i [Azure-guiden till att skapa tjänsteobjekt](../resource-group-authenticate-service-principal.md).
   
    Den här koden går igenom processen att skapa ett Data Lake Store-konto, skapa filer i arkivet, sammanfoga filer, hämta en fil och slutligen att ta bort kontot.
   
        package com.company;
   
        import com.microsoft.azure.CloudException;
        import com.microsoft.azure.credentials.ApplicationTokenCredentials;
        import com.microsoft.azure.management.datalake.store.implementation.DataLakeStoreAccountManagementClientImpl;
        import com.microsoft.azure.management.datalake.store.implementation.DataLakeStoreFileSystemManagementClientImpl;
        import com.microsoft.azure.management.datalake.store.models.*;
        import com.microsoft.azure.management.datalake.store.uploader.*;
        import com.microsoft.rest.credentials.ServiceClientCredentials;
        import java.io.*;
        import java.nio.charset.Charset;
        import java.util.ArrayList;
        import java.util.List;
   
        public class Main {
            final static String ADLS_ACCOUNT_NAME = <DATA-LAKE-STORE-NAME>;
            final static String RESOURCE_GROUP_NAME = "<RESOURCE-GROUP-NAME>";
            final static String LOCATION = "East US 2";
            final static String TENANT_ID = "<TENANT-ID>";
            final static String SUBSCRIPTION_ID =  "<SUBSCRIPTION-ID>";
            final static String CLIENT_ID = "<CLIENT-ID>";
            final static String CLIENT_SECRET = "<CLIENT-SECRET>"; // TODO: For production scenarios, we recommend that you replace this line with a more secure way of acquiring the application client secret, rather than hard-coding it in the source code.
   
            private static DataLakeStoreAccountManagementClientImpl _adlsClient;
            private static DataLakeStoreFileSystemManagementClientImpl _adlsFileSystemClient;
   
            public static void main(String[] args) throws Exception {
                String localFolderPath = "C:\\local_path\\"; // TODO: Change this to any unused, new, empty folder on your local machine.
   
                // Authenticate
                ApplicationTokenCredentials creds = new ApplicationTokenCredentials(CLIENT_ID, TENANT_ID, CLIENT_SECRET, null);
                SetupClients(creds);
   
                // Create Data Lake Store account
                WaitForNewline("Authenticated.", "Creating NEW account.");
                CreateAccount();
                WaitForNewline("Account created.", "Displaying account(s).");
   
                // List Data Lake Store accounts that this app can access
                System.out.println(String.format("All ADL Store accounts that this app can access in subscription %s:", SUBSCRIPTION_ID));
                List<DataLakeStoreAccount> adlsListResult = _adlsClient.accounts().list().getBody();
                for (DataLakeStoreAccount acct : adlsListResult) {
                    System.out.println(acct.name());
                }
                WaitForNewline("Account(s) displayed.", "Uploading file.");
   
                // Upload a file to Data Lake Store: file1.csv
                UploadFile(localFolderPath + "file1.csv", "/file1.csv");
                WaitForNewline("File uploaded.", "Appending newline.");
   
                // Append newline to file1.csv
                AppendToFile("/file1.csv", "\r\n");
                WaitForNewline("Newline appended.", "Creating file.");
   
                // Create a new file in Data Lake Store: file2.csv
                CreateFile("/file2.csv", "456,def", true);
                WaitForNewline("File created.", "Concatenating files.");
   
                // Concatenate two files in Data Lake Store
                List<String> srcFilePaths = new ArrayList<String>();
                srcFilePaths.add("/file1.csv");
                srcFilePaths.add("/file2.csv");
                ConcatenateFiles(srcFilePaths, "/input.csv");
                WaitForNewline("Files concatenated.", "Downloading file.");
   
                // Download file from Data Lake Store
                DownloadFile("/input.csv", localFolderPath + "input.csv");
                WaitForNewline("File downloaded.", "Deleting file.");
   
                // Delete file from Data Lake Store
                DeleteFile("/input.csv");
                WaitForNewline("File deleted.", "Deleting account.");
   
                // Delete account
                DeleteAccount();
                WaitForNewline("Account deleted.", "DONE.");
            }
   
            //Set up clients
            public static void SetupClients(ServiceClientCredentials creds)
            {
                _adlsClient = new DataLakeStoreAccountManagementClientImpl(creds);
                _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClientImpl(creds);
                _adlsClient.withSubscriptionId(SUBSCRIPTION_ID);
            }
   
            // Helper function to show status and wait for user input
            public static void WaitForNewline(String reason, String nextAction)
            {
                if (nextAction == null)
                    nextAction = "";
                if (!nextAction.isEmpty())
                {
                    System.out.println(reason + "\r\nPress ENTER to continue...");
                    try{System.in.read();}
                    catch(Exception e){}
                    System.out.println(nextAction);
                }
                else
                {
                    System.out.println(reason + "\r\nPress ENTER to continue...");
                    try{System.in.read();}
                    catch(Exception e){}
                }
            }
   
            // Create Data Lake Store account
            public static void CreateAccount() throws InterruptedException, CloudException, IOException {
                DataLakeStoreAccount adlsParameters = new DataLakeStoreAccount();
                adlsParameters.withLocation(LOCATION);
   
                _adlsClient.accounts().create(RESOURCE_GROUP_NAME, ADLS_ACCOUNT_NAME, adlsParameters);
            }
   
            // Create file
            public static void CreateFile(String path) throws IOException, AdlsErrorException {
                _adlsFileSystemClient.fileSystems().create(ADLS_ACCOUNT_NAME, path);
            }
   
            // Create file with contents
            public static void CreateFile(String path, String contents, boolean force) throws IOException, AdlsErrorException {
                byte[] bytesContents = contents.getBytes();
   
                _adlsFileSystemClient.fileSystems().create(ADLS_ACCOUNT_NAME, path, bytesContents, force);
            }
   
            // Append to file
            public static void AppendToFile(String path, String contents) throws IOException, AdlsErrorException {
                byte[] bytesContents = contents.getBytes();
   
                _adlsFileSystemClient.fileSystems().append(ADLS_ACCOUNT_NAME, path, bytesContents);
            }
   
            // Concatenate files
            public static void ConcatenateFiles(List<String> srcFilePaths, String destFilePath) throws IOException, AdlsErrorException {
                _adlsFileSystemClient.fileSystems().concat(ADLS_ACCOUNT_NAME, destFilePath, srcFilePaths);
            }
   
            // Delete concatenated file
            public static void DeleteFile(String filePath) throws IOException, AdlsErrorException {
                _adlsFileSystemClient.fileSystems().delete(ADLS_ACCOUNT_NAME, filePath);
            }
   
            // Get file or directory info
            public static FileStatusProperties GetItemInfo(String path) throws IOException, AdlsErrorException {
                return _adlsFileSystemClient.fileSystems().getFileStatus(ADLS_ACCOUNT_NAME, path).getBody().fileStatus();
            }
   
            // List files and directories
            public static List<FileStatusProperties> ListItems(String directoryPath) throws IOException, AdlsErrorException {
                return _adlsFileSystemClient.fileSystems().listFileStatus(ADLS_ACCOUNT_NAME, directoryPath).getBody().fileStatuses().fileStatus();
            }
   
            // Upload file
            public static void UploadFile(String srcPath, String destPath) throws Exception {
                UploadParameters parameters = new UploadParameters(srcPath, destPath, ADLS_ACCOUNT_NAME);
                FrontEndAdapter frontend = new DataLakeStoreFrontEndAdapterImpl(ADLS_ACCOUNT_NAME, _adlsFileSystemClient);
                DataLakeStoreUploader uploader = new DataLakeStoreUploader(parameters, frontend);
                uploader.execute();
            }
   
            // Download file
            public static void DownloadFile(String srcPath, String destPath) throws IOException, AdlsErrorException {
                InputStream stream = _adlsFileSystemClient.fileSystems().open(ADLS_ACCOUNT_NAME, srcPath).getBody();
   
                PrintWriter pWriter = new PrintWriter(destPath, Charset.defaultCharset().name());
   
                String fileContents = "";
                if (stream != null) {
                    Writer writer = new StringWriter();
   
                    char[] buffer = new char[1024];
                    try {
                        Reader reader = new BufferedReader(
                                new InputStreamReader(stream, "UTF-8"));
                        int n;
                        while ((n = reader.read(buffer)) != -1) {
                            writer.write(buffer, 0, n);
                        }
                    } finally {
                        stream.close();
                    }
                    fileContents =  writer.toString();
                }
   
                pWriter.println(fileContents);
                pWriter.close();
            }
   
            // Delete account
            public static void DeleteAccount() throws InterruptedException, CloudException, IOException {
                _adlsClient.accounts().delete(RESOURCE_GROUP_NAME, ADLS_ACCOUNT_NAME);
            }
        }
5. Kör programmet. Följ anvisningarna för att köra och slutföra programmet.

## Nästa steg
* [Säkra data i Data Lake Store](data-lake-store-secure-data.md)
* [Använd Azure Data Lake Analytics med Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

<!--HONumber=sep16_HO1-->


