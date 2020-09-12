---
title: Vanliga frågor om Azure Resource-arbetskraften?
description: Få svar på vanliga frågor om Azure Resource-arbetskraften
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: raynew
ms.openlocfilehash: 520c2d4fd258bfab5a5a1e0abf890d58bb98fbdc
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2020
ms.locfileid: "89653184"
---
# <a name="common-questions"></a>Vanliga frågor

I den här artikeln besvaras vanliga frågor om [Azure Resource-arbetskraft](overview.md).

## <a name="general"></a>Allmänt

### <a name="is-resource-mover-generally-available"></a>Är resurs förflyttningen allmänt tillgänglig?

Resurs förflyttning är för närvarande en offentlig för hands version. Produktions arbets belastningar stöds.

## <a name="region-move"></a>Flytta region

### <a name="what-resources-can-i-move-across-regions-using-resource-mover"></a>Vilka resurser kan jag flytta mellan regioner med resurs förflyttning?

Med hjälp av resurs förflyttning kan du för närvarande flytta följande resurser över flera regioner:

- Virtuella Azure-datorer och tillhör ande diskar
- Nätverkskort
- Tillgänglighetsuppsättningar 
- Virtuella Azure-nätverk 
- Offentliga IP-adresser nätverks säkerhets grupper (NSG: er)
- Interna och offentliga belastningsutjämnare 
- Azure SQL-databaser och elastiska pooler

### <a name="where-is-the-metadata-about-a-region-move-stored"></a>Var lagras metadata om en regions flyttning?

Den lagras i en [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) -databas och i [Azure Blob Storage](../storage/common/storage-service-encryption.md)i en Microsoft-prenumeration.

### <a name="is-the-collected-metadata-encrypted"></a>Har insamlade metadata krypterats?

Ja, både under överföring och i vila.
- Under överföringen skickas metadata på ett säkert sätt till resurs förflyttnings tjänsten via Internet med hjälp av HTTPS.
- Metadata krypteras i lagring.

### <a name="how-is-managed-identity-used-in-resource-mover"></a>Hur används den hanterade identiteten i resurs förflyttningen?

[Hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) (tidigare kallat HANTERAD TJÄNSTIDENTITET (MIS)) tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure AD.
- Resurs förflyttningen använder hanterad identitet så att den kan komma åt Azure-prenumerationer för att flytta resurser mellan regioner.
- En flyttnings samling behöver en tilldelad identitet, med åtkomst till den prenumeration som innehåller de resurser som du flyttar.

- Om du flyttar resurser mellan regioner i portalen sker den här processen automatiskt.
- Om du flyttar resurser med hjälp av PowerShell kör du cmdletar för att tilldela en tilldelad identitet till samlingen och tilldelar sedan en roll med rätt prenumerations behörigheter till identitetens huvud namn. 

### <a name="what-managed-identity-permissions-does-resource-mover-need"></a>Vilka behörigheter för hanterad identitet behöver resurs förflyttningen?

Azure Resource superprocessens hanterade identitet måste ha minst följande behörigheter: 

- Behörighet att skriva/skapa resurser i användar prenumeration, som är tillgängliga med *deltagar* rollen. 
- Behörighet att skapa roll tilldelningar. Är vanligt vis tillgängligt med administratörs rollerna *ägare* eller *användar åtkomst* , eller med en anpassad roll som har tilldelats *behörigheten Microsoft. auktorisering/roll tilldelningar/Skriv* . Den här behörigheten behövs inte om data resurs resursens hanterade identitet redan har beviljats åtkomst till Azure Data Store. 
 
När du lägger till resurser i resurs förflyttnings hubben på portalen hanteras behörigheter automatiskt så länge användaren har de behörigheter som beskrivs ovan. Om du lägger till resurser med PowerShell tilldelar du behörigheter manuellt.

> [!IMPORTANT]
> Vi rekommenderar starkt att du inte ändrar eller tar bort roll tilldelningar för identitet. 

### <a name="what-should-i-do-if-i-dont-have-permissions-to-assign-role-identity"></a>Vad ska jag göra om jag inte har behörighet att tilldela roll identiteten?

**Möjlig orsak** | **Rekommendation**
--- | ---
Du är inte *deltagare* och *administratör för användar åtkomst* (eller *ägare*) när du lägger till en resurs för första gången. | Använd ett konto med behörighet som *deltagare* och *användar åtkomst administratör* (eller *ägare*) för prenumerationen.
Den hanterade identiteten för resurs förflyttad identitet saknar den roll som krävs. | Lägg till rollerna deltagare och användar åtkomst administratör.
Resurs förflyttnings hanterad identitet återställdes till *ingen*. | Återaktivera en systemtilldelad identitet i flytt samlingen > **identitet**. Du kan också lägga till resursen igen i **Lägg till resurser**, vilket gör samma sak.  
Prenumerationen har flyttats till en annan klient. | Inaktivera och aktivera sedan hanterad identitet för flytt samlingen.


## <a name="next-steps"></a>Nästa steg

[Läs mer](about-move-process.md) om resurs förflyttnings komponenter och flyttnings processen.
