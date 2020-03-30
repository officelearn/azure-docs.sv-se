---
title: Felsökning av Azure Data Box för användning av REST-gränssnittet| Microsoft-dokument
description: Beskriver hur du felsöker problem som visas i Azure Data Box när datakopiering sker via REST-gränssnittet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 7fe5afbc4984c430cbf393e4e2b44122bdd43983
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297132"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>Felsöka problem relaterade till Azure Data Box Blob-lagring

I den här artikeln beskrivs information om hur du felsöker problem som kan visas när du använder dataruteblobblagring via REST-gränssnittet i datarutan för att kopiera data. Dessa problem visas när du använder Data Box Blob-lagring med andra program eller klientbibliotek som Azure Storage Explorer, AzCopy eller Azure Storage-bibliotek för Python.

## <a name="errors-seen-in-azure-storage-explorer"></a>Fel som visas i Azure Storage Explorer

I det här avsnittet beskrivs några av problemen när du använder Azure Storage Explorer med Data Box Blob-lagring.

|Felmeddelande  |Rekommenderad åtgärd |
|---------|---------|
|Det gick inte att hämta underordnade resurser. Värdet för ett av HTTP-huvudena är inte i rätt format.|Välj Target Azure **Stack API:er**på **Redigera-menyn** . <br>Starta om Azure Storage Explorer.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |Kontrollera att slutpunktsnamnet `<accountname>.blob.<serialnumber>.microsoftdatabox.com` läggs till i hosts-filen vid den här sökvägen: <li>`C:\Windows\System32\drivers\etc\hosts`i Windows, eller </li><li> `/etc/hosts`på Linux.</li>|
|Det gick inte att hämta underordnade resurser. <br>Detaljer: självsignerat certifikat |Importera TLS/SSL-certifikatet för enheten till Azure Storage Explorer: <li>Hämta certifikatet från Azure-portalen. Mer information finns [i Hämta certifikatet](data-box-deploy-copy-data-via-rest.md#download-certificate).</li><li>Välj **SSL-certifikat** på **Redigera-menyn** och välj sedan **Importera certifikat**.</li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>Fel som visas i AzCopy för Windows

I det här avsnittet beskrivs några av problemen när du använder AzCopy för Windows med databoxblolagring.

|Felmeddelande  |Rekommenderad åtgärd |
|---------|---------|
|AzCopy-kommandot verkar hängas i en minut innan det här felet visas: <br>Det gick inte att räkna upp katalogen https://... Det gick inte att matcha fjärrnamnet`<accountname>.blob.<serialnumber>.microsoftdatabox.com`|Kontrollera att slutpunktsnamnet `<accountname>.blob.<serialnumber>.microsoftdatabox.com` läggs till i `C:\Windows\System32\drivers\etc\hosts`hosts-filen på: .|
|AzCopy-kommandot verkar hängas i en minut innan det här felet visas: <br>Det gick inte att tolka källplatsen. Den underliggande anslutningen stängdes: Det gick inte att upprätta förtroenderelation för ssl/TLS-secure-kanalen.|Importera TLS/SSL-certifikatet för enheten till systemets certifikatarkiv. Mer information finns [i Hämta certifikatet](data-box-deploy-copy-data-via-rest.md#download-certificate).|


## <a name="errors-seen-in-azcopy-for-linux"></a>Fel som visas i AzCopy för Linux

I det här avsnittet beskrivs några av de problem som du har att använda AzCopy för Linux med Data Box Blob-lagring.

|Felmeddelande  |Rekommenderad åtgärd |
|---------|---------|
|AzCopy-kommandot verkar hängas i 20 minuter innan det här felet visas: <br>Det gick inte `https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>`att tolka källplatsen . Ingen sådan anordning eller adress|Kontrollera att slutpunktsnamnet `<accountname>.blob.<serialnumber>.microsoftdatabox.com` läggs till i `/etc/hosts`hosts-filen på: .|
|AzCopy-kommandot verkar hängas i 20 minuter innan det här felet visas: <br>Det gick inte att tolka källplatsen... Det gick inte att upprätta SSL-anslutningen.|Importera TLS/SSL-certifikatet för enheten till systemets certifikatarkiv. Mer information finns [i Hämta certifikatet](data-box-deploy-copy-data-via-rest.md#download-certificate).|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>Fel som visas i Azure Storage-biblioteket för Python

I det här avsnittet beskrivs några av de vanligaste problemen som visas under distributionen av Data Box Disk när du använder en Linux-klient för datakopiering.

|Felmeddelande  |Rekommenderad åtgärd |
|---------|---------|
|Värdet för ett av HTTP-huvudena är inte i rätt format. |Den installerade versionen av Microsoft Azure Storage Library for Python stöds inte av Data Box. Se Azure Data Box Blob-lagringskrav för versioner som stöds.|
|… [SSL: CERTIFICATE_VERIFY_FAILED] ...|Innan du kör Python anger du variabeln REQUESTS_CA_BUNDLE miljö till sökvägen till den Base64-kodade TLS-certifikatfilen (se hur [du hämtar certifikatet](data-box-deploy-copy-data-via-rest.md#download-certificate)). <br>Ett exempel:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>Alternativt lägger du till certifikatet i systemets certifikatarkiv och ställer sedan in den här miljövariabeln på sökvägen till arkivet. <br> Till exempel på Ubuntu: <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>Vanliga fel

Dessa fel är inte specifika för något program.

|Felmeddelande  |Rekommenderad åtgärd |
|---------|---------|
|Anslutningen time out. |Logga in på Data Box-enheten och kontrollera att den är upplåst. Varje gång enheten startas om förblir den låst tills någon loggar in.|

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om kraven för [lagringssystem för databoxblo.](data-box-system-requirements-rest.md)
