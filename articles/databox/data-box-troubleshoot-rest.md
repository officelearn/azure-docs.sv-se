---
title: Azure Data Box felsökning för att använda REST-gränssnittet | Microsoft Docs
description: Beskriver hur du felsöker problem som kan uppstå i Azure Data Box när Datakopieringen är via REST-gränssnittet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: ee2820d78e95924e09a0219753f87d6910c0e736
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60782965"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>Felsöka problem med Azure Data Box Blob-lagring

Den här artikeln detaljerad information om hur du felsöker problem som du kan se när du använder Data Box-Blob-lagringen via REST-gränssnittet på din Data box-enhet för att kopiera data. Dessa problem för angrepp när du använder Data Box-Blob-lagring med andra program eller -klientbiblioteken, till exempel Azure Storage Explorer, AzCopy eller Azure Storage-bibliotek för Python.

## <a name="errors-seen-in-azure-storage-explorer"></a>Fel som visas i Azure Storage Explorer

Det här avsnittet beskriver några av de problem som har när du använder Azure Storage Explorer med blobblagring för Data Box.

|Felmeddelande  |Rekommenderad åtgärd |
|---------|---------|
|Det går inte att hämta underordnade resurser. Värdet för en HTTP-huvuden är inte i rätt format.|Från den **redigera** menyn och välj **Target Azure Stack-API: er**. <br>Starta om Azure Storage Explorer.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |Kontrollera att namnet på slutpunkten `<accountname>.blob.<serialnumber>.microsoftdatabox.com` läggs till i hosts-filen på den här sökvägen: <li>`C:\Windows\System32\drivers\etc\hosts` på Windows, eller </li><li> `/etc/hosts` i Linux.</li>|
|Det går inte att hämta underordnade resurser. <br>Information: självsignerat certifikat |Importera SSL-certifikat för din enhet till Azure Storage Explorer: <li>Hämta certifikatet från Azure-portalen. Mer information går du till [hämta certifikatet](data-box-deploy-copy-data-via-rest.md#download-certificate).</li><li>Från den **redigera** menyn och välj **SSL-certifikat** och välj sedan **Importera certifikat**.</li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>Fel som visas i AzCopy för Windows

Det här avsnittet beskriver några av de problem som har när du använder AzCopy för Windows med blobblagring för Data Box.

|Felmeddelande  |Rekommenderad åtgärd |
|---------|---------|
|AzCopy-kommandot visas låser sig i en minut innan den visas det här felet: <br>Det gick inte att räkna upp directory https://... Fjärrnamnet kunde inte lösas `<accountname>.blob.<serialnumber>.microsoftdatabox.com`|Kontrollera att namnet på slutpunkten `<accountname>.blob.<serialnumber>.microsoftdatabox.com` läggs till i hosts-filen på: `C:\Windows\System32\drivers\etc\hosts`.|
|AzCopy-kommandot visas låser sig i en minut innan den visas det här felet: <br>Fel vid parsning av källplatsen. Den underliggande anslutningen stängdes: Det gick inte att upprätta en förtroenderelation för den säkra SSL/TLS-kanalen.|Importera SSL-certifikat för din enhet till datorns certifikatarkiv. Mer information går du till [hämta certifikatet](data-box-deploy-copy-data-via-rest.md#download-certificate).|


## <a name="errors-seen-in-azcopy-for-linux"></a>Fel som visas i AzCopy för Linux

Det här avsnittet beskriver några av de problem som har när du använder AzCopy för Linux med blobblagring för Data Box.

|Felmeddelande  |Rekommenderad åtgärd |
|---------|---------|
|AzCopy-kommandot visas låser sig på 20 minuter innan den visas det här felet: <br>Fel vid parsning av källplats `https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>`. Det finns ingen sådan enhet eller en adress|Kontrollera att namnet på slutpunkten `<accountname>.blob.<serialnumber>.microsoftdatabox.com` läggs till i hosts-filen på: `/etc/hosts`.|
|AzCopy-kommandot visas låser sig på 20 minuter innan den visas det här felet: <br>Fel vid parsning av källplats... Det gick inte att upprätta en SSL-anslutningen.|Importera SSL-certifikat för din enhet till datorns certifikatarkiv. Mer information går du till [hämta certifikatet](data-box-deploy-copy-data-via-rest.md#download-certificate).|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>Fel som visas i Azure Storage-biblioteket för Python

Det här avsnittet beskriver några av de viktigaste problemen inför under distributionen av Data Box-Disk när du använder en Linux-klient för kopiering av data.

|Felmeddelande  |Rekommenderad åtgärd |
|---------|---------|
|Värdet för en HTTP-huvuden är inte i rätt format. |Den installerade versionen av Microsoft Azure Storage-biblioteket för Python stöds inte av Data Box. Se Azure Data Box Blob lagringskraven för versioner som stöds.|
|… [SSL: CERTIFICATE_VERIFY_FAILED] …|Innan du kör Python, ställa in miljövariabeln REQUESTS_CA_BUNDLE till sökvägen för Base64-kodad SSL-certifikatfil (se hur du [hämta certifikatet]()). <br>Exempel:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>Alternativt kan du lägga till certifikatet i datorns certifikatarkiv och sedan ange variabeln till sökvägen för den store. <br> Till exempel på Ubuntu: <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>Vanliga fel

Dessa fel är inte specifika för alla program.

|Felmeddelande  |Rekommenderad åtgärd |
|---------|---------|
|Tidsgränsen för anslutningen har uppnåtts. |Logga in på Data Box-enheten och kontrollera att det är upplåst. När som helst omstarter av enheten, den är låst tills någon loggar in.|

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om den [Data Box Blob storage-systemkrav](data-box-system-requirements-rest.md).
