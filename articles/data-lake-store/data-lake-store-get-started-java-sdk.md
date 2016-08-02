<properties
   pageTitle="Använd Data Lake Store Java SDK för att utveckla program | Azure"
   description="Använd Azure Data Lake Store Java SDK för att utveckla program"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/12/2016"
   ms.author="nitinme"/>

# Kom igång med Azure Data Lake Store med hjälp av Java

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Lär dig mer om att använda Azure Data Lake Store Java SDK för att skapa ett Azure Data Lake-konto och utföra grundläggande åtgärder som att skapa mappar, ladda upp och hämta filer, ta bort ditt konto, osv. Mer information om Data Lake finns i [Azure Data Lake Store](data-lake-store-overview.md).

## Azure Data Lake Store Java SDK

Följande länkar ger hämtningsplatsen för Java SDK för Data Lake Store och Java SDK-referensen. För de här självstudierna behöver du inte ladda ner SDK eller följa referensdokumentet. Dessa länkar är endast till som information.

* Källkoden för Java SDK för Data Lake Store är tillgänglig på [GitHub](https://github.com/Azure/azure-sdk-for-java).
* Referens för Java SDK för Data Lake Store är tillgänglig på [https://azure.github.io/azure-sdk-for-java/](https://azure.github.io/azure-sdk-for-java/).

## Förutsättningar

* Java Development Kit (JDK) 8 (med Java version 1.8).
* IntelliJ eller en annan lämplig Java Development Environment. Det här är valfritt men rekommenderas. Instruktionerna nedan använder IntelliJ
* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Aktivera din Azure-prenumeration** för en förhandsversion av Data Lake Store. Se [instruktioner](data-lake-store-get-started-portal.md#signup).
* **Skapa ett program i Azure Active Directory**. Det finns två sätt att autentisera med Azure Active Directory – **interaktivt** och **icke-interaktivt**. Det finns olika förutsättningar baserat på hur du vill autentisera.
    * **För interaktiv autentisering** – Du måste skapa ett **Native Client-program** i Azure Active Directory. När du har skapat programmet, hämtar du följande värden som är relaterade till programmet.
        - Hämta **klient-ID** och **omdirigerings-URI** för programmet
        - Ange delegerade behörigheter

    * **För icke-interaktiv autentisering** (används i den här artikeln) – Du måste skapa en **Webbapp** i Azure Active Directory. När du har skapat programmet, hämtar du följande värden som är relaterade till programmet.
        - Hämta **klient-ID**, **klienthemlighet** och **omdirigerings-URI** för programmet
        - Ange delegerade behörigheter
        - Tilldela Azure Active Directory-programmet till en roll. Rollen kan vara för den nivå av omfång för vilken du vill ge behörighet till Azure Active Directory-programmet. Du kan till exempel tilldela programmet på prenumerationsnivån eller på en resursgruppsnivå. 

    Se [Skapa Active Directory-program och tjänstens huvudnamn med hjälp av portalen](../resource-group-create-service-principal-portal.md) för anvisningar om hur du hämtar dessa värden, anger behörigheter och tilldelar roller.

## Hur autentiserar jag med Azure Active Directory?

Kodfragmentet nedan innehåller koden för **icke-interaktiv** autentisering, där programmet tillhandahåller egna autentiseringsuppgifter.

Du måste ge ditt program behörighet att skapa resurser i Azure för att dessa självstudier ska fungera. Det **rekommenderas starkt** att du endast ger det här programmet deltagarbehörighet till en ny, oanvänd och tom resursgrupp i din Azure-prenumeration för självstudierna.

## Skapa ett Java-program

1. Öppna IntelliJ och skapa ett nytt Java-projekt med mallen **Kommandoradsapp**. Slutför guiden för att skapa projektet.

2. Högerklicka på projektet på vänster sida av skärmen och klicka på **Lägg till stöd för Framework**. Välj **Maven** och klicka på **OK**.

3. Öppna den nyligen skapade filen **"pom.xml"** och lägg till följande fragment av text mellan taggen **\</version >** och **\</project>**:

    >[AZURE.NOTE] Det här steget är tillfällig tills Azure Data Lake Store SDK är tillgänglig i Maven. Den här artikeln kommer att uppdateras när SDK är tillgänglig i Maven. Alla framtida uppdateringar till denna SDK blir tillgängliga via Maven.

        <repositories>
            <repository>
                <id>adx-snapshots</id>
                <name>Azure ADX Snapshots</name>
                <url>http://adxsnapshots.azurewebsites.net/</url>
                <layout>default</layout>
                <snapshots>
                    <enabled>true</enabled>
                </snapshots>
            </repository>
            <repository>
                <id>oss-snapshots</id>
                <name>Open Source Snapshots</name>
                <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
                <layout>default</layout>
                <snapshots>
                    <enabled>true</enabled>
                    <updatePolicy>always</updatePolicy>
                </snapshots>
            </repository>
        </repositories>
        <dependencies>
            <dependency>
                <groupId>com.microsoft.azure</groupId>
                <artifactId>azure-client-authentication</artifactId>
                <version>1.0.0-SNAPSHOT</version>
            </dependency>
            <dependency>
                <groupId>com.microsoft.azure</groupId>
                <artifactId>azure-mgmt-datalake-store</artifactId>
                <version>1.0.0-SNAPSHOT</version>
            </dependency>
        </dependencies>


4. Gå till **Arkiv**, sedan **Inställningar**, sedan **Bygga, köra och distribuera**. Expandera **Skapa verktyg**, **Maven** och expandera sedan **Importera**. Markera kryssrutan för **Importera Maven-projekt automatiskt**. Klicka på **Tillämpa** och klicka sedan på **OK**.

5. I den vänstra rutan, gå till **src**, **huvudsakliga**, **java**, **\<paketnamn>** och öppna sedan **Main.java** och ersätt det befintliga kodblocket med följande kod. Ange dessutom värden för parametrarna som påpekas i kodfragmentet såsom **localFolderPath**, **_adlsAccountName**, **_resourceGroupName** och ersätt platshållarna för **CLIENT-ID**, **CLIENT-SECRET**, **TENANT-ID** och **SUBSCRIPTION-ID**.

    Den här koden går igenom processen att skapa ett Data Lake Store-konto, skapa filer i arkivet, sammanfoga filer, hämta en fil och slutligen att ta bort kontot.

        package com.company;
        
        import com.microsoft.azure.CloudException;
        import com.microsoft.azure.credentials.ApplicationTokenCredentials;
        import com.microsoft.azure.management.datalake.store.*;
        import com.microsoft.azure.management.datalake.store.models.*;
        import com.microsoft.rest.credentials.ServiceClientCredentials;
        import java.io.*;
        import java.nio.charset.Charset;
        import java.util.ArrayList;
        import java.util.List;
        
        public class Main {
            private static String _adlsAccountName;
            private static String _resourceGroupName;
            private static String _location;
        
            private static String _tenantId;
            private static String _subId;
            private static String _clientId;
            private static String _clientSecret;
        
            private static DataLakeStoreAccountManagementClient _adlsClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
        
            public static void main(String[] args) throws Exception {
                _adlsAccountName = "<DATA-LAKE-STORE-NAME>";
                _resourceGroupName = "<RESOURCE-GROUP-NAME>";
                _location = "East US 2";
        
                _tenantId = "<TENANT-ID>";
                _subId =  "<SUBSCRIPTION-ID>";
                _clientId = "<CLIENT-ID>";
        
                _clientSecret = "<CLIENT-SECRET>"; // TODO: For production scenarios, we recommend that you replace this line with a more secure way of acquiring the application client secret, rather than hard-coding it in the source code.
        
                String localFolderPath = "C:\\local_path\\"; // TODO: Change this to any unused, new, empty folder on your local machine.
        
                // Authenticate
                ApplicationTokenCredentials creds = new ApplicationTokenCredentials(_clientId, _tenantId, _clientSecret, null);
                SetupClients(creds);
        
                // Create Data Lake Store account
                WaitForNewline("Authenticated.", "Creating NEW account.");
                CreateAccount();
                WaitForNewline("Account created.", "Displaying account(s).");
        
                // List Data Lake Store accounts that this app can access
                System.out.println(String.format("All ADL Store accounts that this app can access in subscription %s:", _subId));
                List<DataLakeStoreAccount> adlsListResult = _adlsClient.getAccountOperations().list().getBody();
                for (DataLakeStoreAccount acct : adlsListResult) {
                    System.out.println(acct.getName());
                }
                WaitForNewline("Account(s) displayed.", "Creating files.");
        
                // Create two files in Data Lake Store: file1.csv and file2.csv
                CreateFile("/file1.csv", "123,abc", true);
                CreateFile("/file2.csv", "456,def", true);
                WaitForNewline("Files created.", "Concatenating files.");
        
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
        
                _adlsClient.setSubscriptionId(_subId);
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
                adlsParameters.setLocation(_location);
        
                _adlsClient.getAccountOperations().create(_resourceGroupName, _adlsAccountName, adlsParameters);
            }
        
            // Create file
            public static void CreateFile(String path) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, path);
            }
        
            // Create file with contents
            public static void CreateFile(String path, String contents, boolean force) throws IOException, CloudException {
                byte[] bytesContents = contents.getBytes();
        
                _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, path, bytesContents, force);
            }
        
            // Append to file
            public static void AppendToFile(String path, String contents) throws IOException, CloudException {
                byte[] bytesContents = contents.getBytes();
        
                _adlsFileSystemClient.getFileSystemOperations().append(_adlsAccountName, path, bytesContents);
            }
        
            // Concatenate files
            public static void ConcatenateFiles(List<String> srcFilePaths, String destFilePath) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().concat(_adlsAccountName, destFilePath, srcFilePaths);
            }
        
            // Delete concatenated file
            public static void DeleteFile(String filePath) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().delete(_adlsAccountName, filePath);
            }
        
            // Get file or directory info
            public static FileStatusProperties GetItemInfo(String path) throws IOException, CloudException {
                return _adlsFileSystemClient.getFileSystemOperations().getFileStatus(_adlsAccountName, path).getBody().getFileStatus();
            }
        
            // List files and directories
            public static List<FileStatusProperties> ListItems(String directoryPath) throws IOException, CloudException {
                return _adlsFileSystemClient.getFileSystemOperations().listFileStatus(_adlsAccountName, directoryPath).getBody().getFileStatuses().getFileStatus();
            }
        
            // Download file
            public static void DownloadFile(String srcPath, String destPath) throws IOException, CloudException {
                InputStream stream = _adlsFileSystemClient.getFileSystemOperations().open(_adlsAccountName, srcPath).getBody();
        
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
                _adlsClient.getAccountOperations().delete(_resourceGroupName, _adlsAccountName);
            }
        }


6. Kör programmet. Följ anvisningarna för att köra och slutföra programmet.

## Nästa steg

- [Säkra data i Data Lake Store](data-lake-store-secure-data.md)
- [Använd Azure Data Lake Analytics med Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Använd Azure HDInsight med Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)



<!--HONumber=Jun16_HO2-->


