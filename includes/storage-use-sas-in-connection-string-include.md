Om du har en URL för delad åtkomst signatur (SAS) som ger åtkomst till resurser i ett lagringskonto kan du använda SAS i en anslutningssträng. Eftersom Signaturen innehåller information som krävs för att autentisera begäran, ger en anslutningssträng med en SAS protokollet, tjänstslutpunkten och autentiseringsuppgifterna som krävs för åtkomst till resursen.

Ange strängen du vill skapa en anslutningssträng som innehåller en signatur för delad åtkomst i följande format:

```
BlobEndpoint=myBlobEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
FileEndpoint=myFileEndpoint;
SharedAccessSignature=sasToken
```

Varje tjänstslutpunkten är valfritt, även om anslutningssträngen måste innehålla minst ett.

> [!NOTE]
> Du bör använda HTTPS med en SAS som bästa praxis.
>
> Om du anger en SAS i en anslutningssträng i en konfigurationsfil, kan du behöva koda specialtecken i URL: en.
>
>

### <a name="service-sas-example"></a>Tjänst-SAS-exempel
Här är ett exempel på en anslutningssträng som innehåller en tjänst-SAS för Blob storage:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&sr=b&si=tutorial-policy-635959936145100803&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

Och här är ett exempel på samma anslutningssträng kodning av specialtecken:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&amp;sr=b&amp;si=tutorial-policy-635959936145100803&amp;sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

### <a name="account-sas-example"></a>Kontot SAS-exempel
Här är ett exempel på en anslutningssträng som innehåller ett SAS-konto för Blob- och lagring. Observera att slutpunkter för båda tjänsterna har angetts:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&spr=https&st=2016-04-12T03%3A24%3A31Z&se=2016-04-13T03%3A29%3A31Z&srt=s&ss=bf&sp=rwl
```

Och här är ett exempel på samma anslutningssträng med URL-kodning:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&amp;sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&amp;spr=https&amp;st=2016-04-12T03%3A24%3A31Z&amp;se=2016-04-13T03%3A29%3A31Z&amp;srt=s&amp;ss=bf&amp;sp=rwl
```

