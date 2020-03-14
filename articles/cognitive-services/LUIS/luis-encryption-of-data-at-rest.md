---
title: Language Understanding tjänst kryptering av data i vila
titleSuffix: Azure Cognitive Services
description: Language Understanding tjänst kryptering av data i vila.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 59e066974f690bda2384504cc27af5aa94b7b75b
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2020
ms.locfileid: "79372341"
---
# <a name="language-understanding-service-encryption-of-data-at-rest"></a>Language Understanding tjänst kryptering av data i vila

Den Language Understanding tjänsten krypterar automatiskt dina data när de sparas i molnet. Krypteringen av den Language Understanding tjänsten skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden.

## <a name="about-cognitive-services-encryption"></a>Om Cognitive Services kryptering

Data krypteras och dekrypteras med hjälp av [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) [-kompatibla 256-bitars AES-](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) kryptering. Kryptering och dekryptering är transparent, vilket innebär att kryptering och åtkomst hanteras åt dig. Dina data är säkra som standard och du behöver inte ändra din kod eller dina program för att utnyttja kryptering.

## <a name="about-encryption-key-management"></a>Om hantering av krypterings nyckel

Som standard använder din prenumeration Microsoft-hanterade krypterings nycklar. Det finns också ett alternativ för att hantera din prenumeration med dina egna nycklar. Kundhanterade nycklar (CMK) ger större flexibilitet för att skapa, rotera, inaktivera och återkalla åtkomst kontroller. Du kan också granska de krypterings nycklar som används för att skydda dina data.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Kundhanterade nycklar med Azure Key Vault

Det finns också ett alternativ för att hantera din prenumeration med dina egna nycklar. Kundhanterade nycklar (CMK), som även kallas för att ta med din egen nyckel (BYOK), erbjuder större flexibilitet för att skapa, rotera, inaktivera och återkalla åtkomst kontroller. Du kan också granska de krypterings nycklar som används för att skydda dina data.

Du måste använda Azure Key Vault för att lagra dina Kundhanterade nycklar. Du kan antingen skapa egna nycklar och lagra dem i ett nyckel valv, eller så kan du använda Azure Key Vault API: er för att generera nycklar. Den Cognitive Services resursen och nyckel valvet måste finnas i samma region och i samma Azure Active Directory-klient (Azure AD), men de kan finnas i olika prenumerationer. Mer information om Azure Key Vault finns i [Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

### <a name="customer-managed-keys-for-language-understanding"></a>Kundhanterade nycklar för Language Understanding

Om du vill begära möjlighet att använda Kundhanterade nycklar, fyller du i och skickar [formuläret Luis service kund-Managed Key Request](https://aka.ms/cogsvc-cmk). Det tar cirka 3-5 arbets dagar att höra om status för din begäran. Beroende på efter frågan kan du placera i en kö och godkännas som utrymme blir tillgängligt. När du har godkänt för att använda CMK med LUIS måste du skapa en ny Language Understanding resurs från Azure Portal och välja E0 som pris nivå. Den nya SKU: n fungerar på samma sätt som F0 SKU som redan är tillgänglig förutom för CMK. Användarna kommer inte att kunna uppgradera från F0 till den nya E0 SKU: n.

E0-resurser är bara tillgängliga för redigerings tjänsten och att E0-nivån först kommer att stödjas i regionen Västra USA.

![LUIS prenumerations avbildning](../media/cognitive-services-encryption/luis-subscription.png)

### <a name="regional-availability"></a>Regional tillgänglighet

Kundhanterade nycklar är för närvarande tillgängliga i regionen **USA, västra** .

### <a name="limitations"></a>Begränsningar

Det finns vissa begränsningar när du använder E0-nivån med befintliga/tidigare skapade program:

* Migreringen till en E0-resurs kommer att blockeras. Användarna kommer bara att kunna migrera sina appar till F0-resurser. När du har migrerat en befintlig resurs till F0 kan du skapa en ny resurs i E0-nivån. Läs mer om [migrering här](https://docs.microsoft.com/azure/cognitive-services/luis/luis-migration-authoring).  
* Att flytta program till eller från en E0-resurs kommer att blockeras. Ett sätt att undvika den här begränsningen är att exportera det befintliga programmet och importera det som en E0-resurs.
* Stavnings kontroll funktionen i Bing stöds inte.
* Loggning av slut användar trafik är inaktive rad om ditt program är E0.
* Tal Prima-funktionen från Azure bot service stöds inte för program i E0-nivån. Den här funktionen är tillgänglig via Azure Bot Service, som inte stöder CMK.
* Tal Prima-funktionen från portalen kräver Azure Blob Storage. Mer information finns i [ta med din egen lagring](../Speech-Service/speech-encryption-of-data-at-rest.md#bring-your-own-storage-byos-for-customization-and-logging).

### <a name="enable-customer-managed-keys"></a>Aktivera Kundhanterade nycklar

En ny Cognitive Services resurs krypteras alltid med Microsoft-hanterade nycklar. Det går inte att aktivera Kundhanterade nycklar vid den tidpunkt då resursen skapas. Kundhanterade nycklar lagras i Azure Key Vault och nyckel valvet måste tillhandahållas med åtkomst principer som ger nyckel behörigheter till den hanterade identitet som är kopplad till den Cognitive Services resursen. Den hanterade identiteten är bara tillgänglig när resursen har skapats med hjälp av pris nivån för CMK.

Information om hur du använder Kundhanterade nycklar med Azure Key Vault för Cognitive Services kryptering finns i:

- [Konfigurera Kundhanterade nycklar med Key Vault för Cognitive Services kryptering från Azure Portal](../Encryption/cognitive-services-encryption-keys-portal.md)

Om du aktiverar Kundhanterade nycklar aktive ras även en systemtilldelad hanterad identitet, en funktion i Azure AD. När systemtilldelad hanterad identitet har Aktiver ATS registreras den här resursen med Azure Active Directory. När den hanterade identiteten har registrerats får den åtkomst till den Key Vault som valts under kund hanterade nyckel inställningar. Du kan läsa mer om [hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Om du inaktiverar systemtilldelade hanterade identiteter tas åtkomst till nyckel valvet bort och alla data som krypteras med kund nycklarna kommer inte längre att vara tillgängliga. Alla funktioner som är beroende av dessa data upphör att fungera.

> [!IMPORTANT]
> Hanterade identiteter stöder för närvarande inte scenarier mellan kataloger. När du konfigurerar Kundhanterade nycklar i Azure Portal tilldelas en hanterad identitet automatiskt under försättsblad. Om du senare flyttar prenumerationen, resurs gruppen eller resursen från en Azure AD-katalog till en annan överförs inte den hanterade identitet som är kopplad till resursen till den nya klienten, så Kundhanterade nycklar kanske inte längre fungerar. Mer information finns i **överföra en prenumeration mellan Azure AD-kataloger** i [vanliga frågor och svar och kända problem med hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Lagra Kundhanterade nycklar i Azure Key Vault

Om du vill aktivera Kundhanterade nycklar måste du använda en Azure Key Vault för att lagra dina nycklar. Du måste aktivera både den **mjuka borttagningen** och **Rensa inte** egenskaperna i nyckel valvet.

Endast RSA-nycklar med storleken 2048 stöds med Cognitive Services kryptering. Mer information om nycklar finns **Key Vault nycklar** i [om Azure Key Vault nycklar, hemligheter och certifikat](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

### <a name="rotate-customer-managed-keys"></a>Rotera Kundhanterade nycklar

Du kan rotera en kundhanterad nyckel i Azure Key Vault enligt efterlevnadsprinciper. När nyckeln roteras måste du uppdatera Cognitive Services-resursen för att använda den nya nyckel-URI: n. Information om hur du uppdaterar resursen för att använda en ny version av nyckeln i Azure Portal finns i avsnittet **Uppdatera nyckel versionen** i [Konfigurera kund hanterade nycklar för Cognitive Services med hjälp av Azure Portal](../Encryption/cognitive-services-encryption-keys-portal.md).

Rotation av nyckeln utlöser inte Omkryptering av data i resursen. Det krävs ingen ytterligare åtgärd från användaren.

### <a name="revoke-access-to-customer-managed-keys"></a>Återkalla åtkomst till Kundhanterade nycklar

Om du vill återkalla åtkomsten till Kundhanterade nycklar använder du PowerShell eller Azure CLI. Mer information finns i [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) eller [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Att återkalla åtkomsten på ett effektivt sätt blockerar åtkomsten till alla data i Cognitive Services resursen, eftersom krypterings nyckeln inte är tillgänglig via Cognitive Services.

## <a name="next-steps"></a>Nästa steg

* [LUIS service kund – hanterad nyckel förfrågnings formulär](https://aka.ms/cogsvc-cmk)
* [Läs mer om Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
