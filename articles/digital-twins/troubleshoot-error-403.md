---
title: 'Azure Digitals dubblare-begäran misslyckades med status: 403 (tillåts inte)'
description: 'Det gick inte att utföra service förfrågningar. Status: 403 (ej tillåtet) på Azure Digital-dubbla.'
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 7/20/2020
ms.openlocfilehash: 95372d69f58249afc946f12ec74696507429e5c6
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2020
ms.locfileid: "87125315"
---
# <a name="service-request-failed-status-403-forbidden"></a>Tjänstbegäran misslyckades. Status: 403 (ej tillåtet)

I den här artikeln beskrivs orsaker och lösnings steg för att ta emot ett 403-fel från tjänst begär anden till Azure Digitals. 

## <a name="symptoms"></a>Symtom

Det här felet kan inträffa på många typer av tjänst begär Anden som kräver autentisering. Detta är att API-begäran Miss lyckas, vilket returnerar fel statusen `403 (Forbidden)` .

## <a name="causes"></a>Orsaker

### <a name="cause-1"></a>Orsak #1

Oftast anger det här felet att RBAC-behörighet (rollbaserad åtkomst kontroll) för tjänsten inte har ställts in korrekt. Många åtgärder för en digital Azure Digital-instans kräver att du har rollen *Azure Digitals-ägare (förhands granskning)* **på den instans som du försöker hantera**. 

### <a name="cause-2"></a>Orsak #2

Om du använder en-klient för att kommunicera med Azure Digitals, kan det här felet inträffa eftersom din [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) -appens registrering inte har behörighet att konfigurera för Azure Digitals-tjänsten.

Appens registrering krävs för att få åtkomst behörighet som kon figurer ATS för Azure Digitals dubbla API: er. När klient programmet autentiserar mot appens registrering beviljas den de behörigheter som appens registrering har konfigurerat.

## <a name="solutions"></a>Lösningar

### <a name="solution-1"></a>Lösnings #1

Den första lösningen är att kontrol lera att Azure-användaren har rollen _**Azure Digital-ägare (förhands granskning)**_ på den instans som du försöker hantera. Om du inte har den här rollen ställer du in den.

Observera att rollen skiljer sig från...
* *ägar* rollen för hela Azure-prenumerationen. *Azure Digitals flätat-ägare (för hands version)* är en roll i Azure Digitals och är begränsad till den här enskilda Azure Digital-instansen.
* *ägar* rollen i Azure Digitals dubbla. Detta är två unika hanterings roller för Azure Digitals, och *Azure Digitals-ägare (för hands version)* är den roll som ska användas för hantering under för hands versionen.

#### <a name="check-current-setup"></a>Kontrol lera aktuell installation

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>Åtgärda problem 

Om du inte har den här roll tilldelningen ska någon med en ägar roll i din **Azure-prenumeration** köra följande kommando för att ge Azure-användaren rollen *Azure Digitals-ägare (för hands version)* på **Azure Digitals-instansen**. 

Om du är ägare till prenumerationen kan du köra det här kommandot själv. Om du inte gör det kan du kontakta en ägare för att köra det här kommandot åt dig.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "Azure Digital Twins Owner (Preview)"
```

Mer information om det här roll kravet och tilldelnings processen finns i avsnittet [ *Konfigurera din användares åtkomst behörigheter* ](how-to-set-up-instance-manual.md#set-up-your-users-access-permissions) i *instruktion: Konfigurera en instans och autentisering (manuell)*.

Om du redan har den här roll tilldelningen och fortfarande stöter på 403-problemet kan du fortsätta till nästa lösning.

### <a name="solution-2"></a>Lösnings #2

Den andra lösningen är att kontrol lera att Azure AD-appens registrering har behörigheter som har kon figurer ATS för tjänsten Azure Digitals dubbla. Om detta inte är konfigurerat ställer du in dem.

#### <a name="check-current-setup"></a>Kontrol lera aktuell installation

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Kontrol lera först att inställningarna för Azure Digitals-inställningar för digital har kon figurer ATS korrekt i registreringen. Det gör du genom att välja *manifest* från meny raden för att visa appens registrerings manifest kod. Bläddra till slutet av kod fönstret och leta efter dessa fält under `requiredResourceAccess` . Värdena ska matcha dem i skärm bilden nedan:

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

#### <a name="fix-issues"></a>Åtgärda problem

Om något av detta ser annorlunda ut än det som beskrivs följer du anvisningarna om hur du konfigurerar en app-registrering i [avsnittet *Konfigurera åtkomst behörigheter för klient program* ](how-to-set-up-instance-manual.md#set-up-access-permissions-for-client-applications) i *anvisningar: Konfigurera en instans och autentisering (manuell)*.

## <a name="next-steps"></a>Nästa steg

Läs installations stegen för att skapa och autentisera en ny Azure Digital-instansen:
* [*Anvisningar: Konfigurera en instans och autentisering (manuell)*](how-to-set-up-instance-manual.md)

Läs mer om säkerhet och behörigheter på digitala Azure-objekt:
* [*Koncept: säkerhet för Azure Digitals dubbla lösningar*](concepts-security.md)