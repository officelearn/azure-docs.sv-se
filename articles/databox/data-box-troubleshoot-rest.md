---
title: Azure Data Box fel sökning för användning av REST-gränssnittet | Microsoft Docs
description: Beskriver hur du felsöker problem som visas i Azure Data Box när data kopiering sker via REST-gränssnittet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 7fe5afbc4984c430cbf393e4e2b44122bdd43983
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80297132"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>Felsöka problem som rör Azure Data Box Blob Storage

Den här artikeln innehåller information om hur du felsöker problem som kan uppstå när du använder Data Box-enhet blob-lagring via REST-gränssnittet på din Data Box-enhet för att kopiera data. Dessa problem kan uppstå när du använder Data Box-enhet Blob Storage med andra program eller klient bibliotek som Azure Storage Explorer, AzCopy eller Azure Storage bibliotek för python.

## <a name="errors-seen-in-azure-storage-explorer"></a>Fel som visas i Azure Storage Explorer

I det här avsnittet beskrivs några av de problem som finns när du använde Azure Storage Explorer med Data Box-enhet Blob Storage.

|Felmeddelande  |Rekommenderad åtgärd |
|---------|---------|
|Det gick inte att hämta underordnade resurser. Värdet för ett av HTTP-huvudena har fel format.|I **Redigera** -menyn väljer du **mål Azure Stack API: er**. <br>Starta om Azure Storage Explorer.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |Kontrol lera att slut punkts namnet `<accountname>.blob.<serialnumber>.microsoftdatabox.com` har lagts till i hosts-filen på den här sökvägen: <li>`C:\Windows\System32\drivers\etc\hosts`i Windows eller </li><li> `/etc/hosts`i Linux.</li>|
|Det gick inte att hämta underordnade resurser. <br>Information: självsignerat certifikat |Importera TLS/SSL-certifikatet för enheten till Azure Storage Explorer: <li>Hämta certifikatet från Azure Portal. Mer information finns i [Hämta certifikatet](data-box-deploy-copy-data-via-rest.md#download-certificate).</li><li>Välj **SSL-certifikat** på **Redigera** -menyn och välj sedan **Importera certifikat**.</li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>Fel som visas i AzCopy för Windows

I det här avsnittet beskrivs några av de problem som finns när du använder AzCopy för Windows med Data Box-enhet Blob Storage.

|Felmeddelande  |Rekommenderad åtgärd |
|---------|---------|
|AzCopy-kommandot verkar låsa sig en minut innan det här felet visas: <br>Det gick inte att räkna upp katalogens https://... Det gick inte att matcha fjärrnamnet`<accountname>.blob.<serialnumber>.microsoftdatabox.com`|Kontrol lera att slut punkts namnet `<accountname>.blob.<serialnumber>.microsoftdatabox.com` läggs till i hosts- `C:\Windows\System32\drivers\etc\hosts`filen på:.|
|AzCopy-kommandot verkar låsa sig en minut innan det här felet visas: <br>Fel vid parsning av käll plats. Den underliggande anslutningen stängdes: det gick inte att upprätta en förtroende relation för den säkra SSL/TLS-kanalen.|Importera TLS/SSL-certifikatet för enheten till systemets certifikat arkiv. Mer information finns i [Hämta certifikatet](data-box-deploy-copy-data-via-rest.md#download-certificate).|


## <a name="errors-seen-in-azcopy-for-linux"></a>Fel som visas i AzCopy för Linux

I det här avsnittet beskrivs några av de problem som finns när du använder AzCopy för Linux med Data Box-enhet Blob Storage.

|Felmeddelande  |Rekommenderad åtgärd |
|---------|---------|
|AzCopy-kommandot verkar låsa sig i 20 minuter innan det här felet visas: <br>Fel vid parsning av käll `https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>`plats. Ingen sådan enhet eller adress|Kontrol lera att slut punkts namnet `<accountname>.blob.<serialnumber>.microsoftdatabox.com` läggs till i hosts- `/etc/hosts`filen på:.|
|AzCopy-kommandot verkar låsa sig i 20 minuter innan det här felet visas: <br>Fel vid parsning av käll plats... Det gick inte att upprätta SSL-anslutningen.|Importera TLS/SSL-certifikatet för enheten till systemets certifikat arkiv. Mer information finns i [Hämta certifikatet](data-box-deploy-copy-data-via-rest.md#download-certificate).|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>Fel som visas i Azure Storage bibliotek för python

I det här avsnittet beskrivs några av de vanligaste problemen vid distribution av Data Box Disk när du använder en Linux-klient för data kopiering.

|Felmeddelande  |Rekommenderad åtgärd |
|---------|---------|
|Värdet för ett av HTTP-huvudena har fel format. |Den installerade versionen av Microsoft Azure Storages biblioteket för python stöds inte av Data Box-enhet. Se Azure Data Box Blob Storage-krav för versioner som stöds.|
|… [SSL: CERTIFICATE_VERIFY_FAILED]...|Innan du kör python ställer du in REQUESTS_CA_BUNDLE miljövariabeln till sökvägen till den base64-kodade TLS-certifikatarkiven (se hur du [hämtar certifikatet](data-box-deploy-copy-data-via-rest.md#download-certificate)). <br>Ett exempel:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>Alternativt kan du lägga till certifikatet i systemets certifikat Arkiv och sedan ange miljövariabeln till sökvägen för arkivet. <br> Till exempel på Ubuntu: <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>Vanliga fel

Dessa fel är inte specifika för något program.

|Felmeddelande  |Rekommenderad åtgärd |
|---------|---------|
|Anslutnings tiden är slut. |Logga in på Data Box-enhet enheten och kontrol lera att den är upplåst. När enheten startas om förblir den låst tills någon loggar in.|

## <a name="next-steps"></a>Nästa steg

- Läs mer om [system kraven för data Box-enhet Blob Storage](data-box-system-requirements-rest.md).
