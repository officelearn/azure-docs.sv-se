---
title: Uppdatera lösen ordet för AD DS Storage-konto
description: Lär dig hur du uppdaterar lösen ordet för det Active Directory Domain Services konto som representerar ditt lagrings konto. Detta förhindrar att lagrings kontot rensas när lösen ordet upphör att gälla, vilket förhindrar autentiseringsfel.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: rogarana
ms.openlocfilehash: 936580f19d7370f72ebdc52cb39f9f5e1cb5984d
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84268459"
---
# <a name="update-the-password-of-your-storage-account-identity-in-ad-ds"></a>Uppdatera lösen ordet för din lagrings konto identitet i AD DS

Om du har registrerat den Active Directory Domain Services (AD DS) som representerar ditt lagrings konto i en organisationsenhet eller domän som tillämpar lösen ordets giltighets tid, måste du ändra lösen ordet innan du anger den högsta ålder för lösen ord. Din organisation kan köra automatiserade rensnings skript som tar bort konton när deras lösen ord upphör att gälla. På grund av detta kan ditt konto, om du inte ändrar ditt lösen ord innan det går ut, tas bort, vilket gör att du förlorar åtkomsten till dina Azure-filresurser.

Om du vill utlösa lösen ords rotation kan du köra `Update-AzStorageAccountADObjectPassword` kommandot från [AzFilesHybrid-modulen](https://github.com/Azure-Samples/azure-files-samples/releases). Det här kommandot måste köras i en lokal AD DS-ansluten miljö med hjälp av en hybrid användare med ägar behörighet till lagrings kontot och AD DS-behörigheter för att ändra lösen ordet för den identitet som representerar lagrings kontot. Kommandot utför åtgärder som liknar lagrings kontots nyckel rotation. Mer specifikt hämtar den andra Kerberos-nyckeln för lagrings kontot och använder den för att uppdatera lösen ordet för det registrerade kontot i AD DS. Sedan återskapas mål-Kerberos-nyckeln för lagrings kontot och lösen ordet för det registrerade kontot uppdateras i AD DS. Du måste köra det här kommandot i en lokal AD DS-ansluten miljö.

```PowerShell
# Update the password of the AD DS account registered for the storage account
# You may use either kerb1 or kerb2
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```
