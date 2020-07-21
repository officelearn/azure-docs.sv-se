---
title: Kända problem – Azure Digitals, dubbla
description: Få hjälp med att identifiera och åtgärda kända problem med Azure Digitals.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: 8fce451d9b806d2fa9a4f3d9e1c117de0aaa9fc0
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86532294"
---
# <a name="known-issues-in-azure-digital-twins"></a>Kända problem i Azure Digitals, dubbla

Den här artikeln innehåller information om kända problem som är associerade med Azure Digitals.

## <a name="managing-event-routes-in-the-azure-portal"></a>Hantera händelse vägar i Azure Portal

Om du är inloggad på portalen med en personlig [**Microsoft-konto (MSA)**](https://account.microsoft.com/account/Account), till exempel ett *@outlook.com* konto, ser du en skärm som säger *att du måste ha behörighet att visa händelse vägar när du* försöker hantera händelse vägar i portalen, oavsett vilken behörighets nivå som används.

:::image type="content" source="media/troubleshoot-known-issues/event-route-need-permission.png" alt-text="Skärm bild från Azure Portal av behörighets fel vid försök att skapa händelse vägar på en digital Azure-instans":::

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

Användare som för närvarande inte kan hantera händelse vägar i portalen kan fortfarande hantera händelse vägar med hjälp av Azure Digitals dubbla API: er eller CLI. Att växla till ett av dessa verktyg för hantering av händelse vägar är den rekommenderade strategin för att undvika det här problemet.

Instruktionerna för detta finns i [*instruktion: hantera slut punkter och vägar*](how-to-manage-routes.md).

### <a name="possible-causes"></a>Möjliga orsaker

Du är inloggad på portalen med en personlig [Microsoft-konto (MSA)](https://account.microsoft.com/account/Account), till exempel ett *@outlook.com* konto. Hantering av händelse vägar i Azure Portal är för närvarande bara tillgängligt för Azure-användare på företags domän konton.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>"400-klient fel: felaktig begäran" i Cloud Shell

Kommandon i Cloud Shell kan ibland Miss lyckas med fel meddelandet "400-klient fel: felaktig begäran för URL: http://localhost:50342/oauth2/token " följt av fullständig stack spårning.

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

Detta kan lösas genom att köra kommandot igen `az login` och slutföra efterföljande inloggnings steg.

Sedan bör du kunna köra kommandot igen.

### <a name="possible-causes"></a>Möjliga orsaker

Detta är resultatet av ett känt problem i Cloud Shell: [*hämtning av token från Cloud Shell Miss lyckas tillfälligt med 400-klient fel: felaktig begäran*](https://github.com/Azure/azure-cli/issues/11749).

## <a name="next-steps"></a>Nästa steg

Läs mer om säkerhet och behörigheter på digitala Azure-objekt:
* [*Koncept: säkerhet för Azure Digitals dubbla lösningar*](concepts-security.md)