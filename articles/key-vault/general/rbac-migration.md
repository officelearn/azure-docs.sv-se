---
title: Migrera till rollbaserad åtkomst kontroll i Azure | Microsoft Docs
description: Lär dig hur du migrerar från valv åtkomst principer till Azure-roller.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.openlocfilehash: 23a36bfc048a6214ccb79b793a23c21d5f8e305e
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288265"
---
# <a name="migrate-from-vault-access-policy-to-an-azure-role-based-access-control-preview-permission-model"></a>Migrera från valv åtkomst princip till en Azure rollbaserad åtkomst kontroll (för hands version) behörighets modell

Valv åtkomst princip modellen är ett befintligt auktoriserings system som skapats i Key Vault för att ge åtkomst till nycklar, hemligheter och certifikat. Du kan kontrol lera åtkomst genom att tilldela säkerhets objekt behörigheter (användare, grupp, tjänstens huvud namn, hanterad identitet) i Key Vault omfattning. 

Rollbaserad åtkomst kontroll i Azure (Azure RBAC) är ett auktoriserings system som bygger på [Azure Resource Manager](../../azure-resource-manager/management/overview.md) och ger detaljerad åtkomst hantering av Azure-resurser. Azure RBAC för Key Vault nycklar, hemligheter och certifikat åtkomst hantering är för närvarande en offentlig för hands version. Med Azure RBAC styr du åtkomsten till resurser genom att skapa roll tilldelningar som består av tre element: säkerhets objekt, roll definition (fördefinierad uppsättning behörigheter) och omfattning (grupp resurser eller enskild resurs). Mer information finns i [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md).

Innan du migrerar till Azure RBAC är det viktigt att förstå dess fördelar och begränsningar.

Fördelar med Azure RBAC-nyckel över valv åtkomst principer:
- Tillhandahåller enhetlig åtkomst kontroll modell för Azure-resurser – samma API i Azure-tjänster
- Centraliserad åtkomst hantering för administratörer – hantera alla Azure-resurser i en vy
- Integrerad med [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) för tidsbaserad åtkomst kontroll
- Neka tilldelningar – möjlighet att undanta säkerhets objekt vid särskilda omfång. Mer information finns i [förstå Azure Deny-tilldelningar](../../role-based-access-control/deny-assignments.md)

Azure RBAC-nack delar:
- Svars tid för roll tilldelningar – det kan ta flera minuter innan roll tilldelningen tillämpas. Åtkomst principer för valv tilldelas direkt.
- Begränsat antal roll tilldelningar – 2000 roll tilldelningar per prenumeration respektive 1024 åtkomst principer per Key Vault

## <a name="access-policies-to-azure-roles-mapping"></a>Åtkomst principer till Azure roles-mappning

Azure RBAC har flera inbyggda Azure-roller som du kan tilldela till användare, grupper, tjänstens huvud namn och hanterade identiteter. Om de inbyggda rollerna inte uppfyller organisationens specifika behov kan du skapa egna [Azure-anpassade roller](../../role-based-access-control/custom-roles.md).

Key Vault inbyggda roller för nycklar, certifikat och åtkomst hantering för hemligheter:
- Key Vault administratör (förhands granskning)
- Key Vault läsare (förhands granskning)
- Key Vault certifikat ansvarig (förhands granskning)
- Key Vault kryptografi utanordnare (förhands granskning)
- Key Vault krypto-användare (förhands granskning)
- Key Vault hemligheter (förhands granskning)
- Key Vault hemligheter, användare (förhands granskning)

Mer information om befintliga inbyggda roller finns i [inbyggda roller i Azure](../../role-based-access-control/built-in-roles.md)

Valv åtkomst principer kan tilldelas med individuellt valda behörigheter eller med fördefinierade behörighets mallar.

Åtkomst principer fördefinierade behörighets mallar:
- Nyckel, hemlighet, certifikat hantering
- Nyckel & hemlig hantering
- Hantering av hemliga & certifikat
- Nyckel hantering
- Hemlig hantering
- Certifikathantering
- SQL Server-anslutning
- Azure Data Lake Storage eller Azure Storage
- Azure Backup
- Kund nyckel för Exchange Online
- Kund nyckel för SharePoint Online
- Azure information-BYOK

### <a name="access-policies-templates-to-azure-roles-mapping"></a>Mallar för åtkomst principer till Azure roles-mappning
| Mall för åtkomst princip | Operations | Azure-roll |
| --- | --- | --- |
| Nyckel, hemlighet, certifikat hantering | Nycklar: alla åtgärder <br>Certifikat: alla åtgärder<br>Hemligheter: alla åtgärder | Key Vault administratör (förhands granskning) |
| Nyckel & hemlig hantering | Nycklar: alla åtgärder <br>Hemligheter: alla åtgärder| Key Vault kryptografi utanordnare (förhands granskning)<br> Key Vault hemligheter (förhands granskning)|
| Hantering av hemliga & certifikat | Certifikat: alla åtgärder <br>Hemligheter: alla åtgärder| Key Vault certifikat ansvarig (förhands granskning)<br> Key Vault hemligheter (förhands granskning)|
| Nyckel hantering | Nycklar: alla åtgärder| Key Vault kryptografi utanordnare (förhands granskning)|
| Hemlig hantering | Hemligheter: alla åtgärder| Key Vault hemligheter (förhands granskning)|
| Certifikathantering | Certifikat: alla åtgärder | Key Vault certifikat ansvarig (förhands granskning)|
| SQL Server-anslutning | Nycklar: get, list, wrapping Key, unwrap Key | Key Vault kryptering av krypterings tjänst (för hands version)|
| Azure Data Lake Storage eller Azure Storage | Nycklar: get, list och unwrap Key | E.t.<br> Anpassad roll krävs|
| Azure Backup | Nycklar: get, list, backup<br> Certifikat: Hämta, lista, säkerhetskopiera | E.t.<br> Anpassad roll krävs|
| Kund nyckel för Exchange Online | Nycklar: get, list, wrapping Key, unwrap Key | Key Vault kryptering av krypterings tjänst (för hands version)|
| Kund nyckel för Exchange Online | Nycklar: get, list, wrapping Key, unwrap Key | Key Vault kryptering av krypterings tjänst (för hands version)|
| Azure information-BYOK | Nycklar: Hämta, dekryptera, signera | E.t.<br>Anpassad roll krävs|


## <a name="assignment-scopes-mapping"></a>Mappning av tilldelnings omfång  

Azure RBAC för Key Vault tillåter roll tilldelning i följande omfattningar:
- Hanteringsgrupp
- Prenumeration
- Resursgrupp
- Key Vault resurs
- Individuell nyckel, hemlighet och certifikat

Åtkomst princips modellen för valv är begränsad till att endast tilldela principer på Key Vault resurs nivå, som 

I allmänhet är det bästa praxis att ha ett nyckel valv per program och hantera åtkomst på nyckel valv nivå. Det finns scenarier när du hanterar åtkomst i andra scope för att förenkla åtkomst hanteringen.

- * * Infrastruktur, säkerhets administratörer och operatörer: att hantera grupper av nyckel valv i hanterings grupp, prenumerations-eller resurs grupps nivå med åtkomst principer för valv kräver att principer för varje nyckel valv upprätthålls. Med Azure RBAC kan du skapa en roll tilldelning i hanterings grupp, prenumeration eller resurs grupp. Tilldelningen gäller alla nya nyckel valv som skapats i samma omfång. I det här scenariot rekommenderar vi att du använder Privileged Identity Management med just-in-Time-åtkomst för att ge permanent åtkomst.
 
- * * Program: det finns scenarier när programmet skulle behöva dela hemligheten med andra program. Det måste skapas ett separat nyckel valv för att undvika att ge åtkomst till alla hemligheter för att använda valv åtkomst principer. Med Azure RBAC kan du tilldela en roll med omfång för enskild hemlighet i stället för ett enda nyckel valv.

## <a name="vault-access-policy-to-azure-rbac-migration-steps"></a>Åtkomst princip för valv till Azure RBAC-migrering
Det finns många skillnader mellan Azure RBAC-och valv åtkomst princip behörighets modell. För att undvika avbrott under migreringen bör du följa stegen nedan.
 
1. **Identifiera och tilldela roller** : identifiera inbyggda roller baserat på mappnings tabellen ovan och skapa anpassade roller vid behov. Tilldela roller i omfattningar baserat på vägledning för mappning av omfattningar. Mer information om hur du tilldelar roller till Key Vault finns i [ge åtkomst till Key Vault med en rollbaserad åtkomst kontroll i Azure (för hands version)](rbac-guide.md)
1. **Verifiera roll tilldelning** : roll tilldelningar i Azure RBAC kan ta flera minuter att sprida. Information om hur du kontrollerar roll tilldelningar finns i [lista roll tilldelningar i omfång](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope)
1. **Konfigurera övervakning och avisering i nyckel valvet** : det är viktigt att aktivera loggning och konfigurera aviseringar för åtkomst nekade undantag. Mer information finns i [övervakning och aviseringar för Azure Key Vault](./alert.md)
1. **Ange behörighets modell för Azure-rollbaserad åtkomst kontroll på Key Vault: om** du aktiverar Azure RBAC-behörighets modell kommer alla befintliga åtkomst principer att ogiltig förklaras. Om ett fel uppstår kan behörighets modellen växlas tillbaka med alla befintliga åtkomst principer kvar.

> [!NOTE]
> När Azure RBAC-behörigheten är aktive rad kommer alla skript som försöker uppdatera åtkomst principer att Miss lyckas. Det är viktigt att uppdatera dessa skript för att använda Azure RBAC.

## <a name="troubleshooting"></a>Felsökning
-  Roll tilldelningen fungerar inte efter flera minuter – det finns situationer då roll tilldelningarna kan ta längre tid. Det är viktigt att skriva omprövnings logik i koden för att avse dessa fall.
- Roll tilldelningar försvann när Key Vault togs bort (mjuk borttagning) och återställs – den är för närvarande en begränsning av funktionen för mjuk borttagning i alla Azure-tjänster. Det krävs att du återskapar alla roll tilldelningar efter återställningen.    

## <a name="learn-more"></a>Mer information

- [Översikt över Azure RBAC](../../role-based-access-control/overview.md)
- [Själv studie kurs om anpassade roller](../../role-based-access-control/tutorial-custom-role-cli.md)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)