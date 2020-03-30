---
title: Hanteringsverktyg för Windows Virtual Desktop – Azure
description: Felsöka problem med hanteringsverktyget för Virtuellt skrivbord i Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 11/09/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9d07ba42e83d9eec071ab047e9e1e92bac1f1411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127482"
---
# <a name="troubleshoot-the-windows-virtual-desktop-management-tool"></a>Felsöka Windows Virtual Desktop-hanteringsverktyget

I den hÃ¤r artikeln beskrivs problem som kan uppstÃ¥ vid distribution av hanteringsverktyget för Windows Virtual Desktop och hur du åtgärdar dem.

## <a name="error-management-tool-services-configured-but-automated-setup-fails"></a>Fel: Hanteringsverktygstjänster konfigurerade men automatisk installation misslyckas

NÃ¤r du har konfigurerat tjänster fÃ¶r hanteringsverktyget men automatisk installation misslyckas visas det fÃ¶rsÃ¤gg:

```console
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n "status": "Failed",\r\n "error": {\r\n "code": "ResourceDeploymentFailure",\r\n "message": "The resource operation completed with terminal provisioning state 'Failed'."\r\n }\r\n}"}]}
```

Detta innebär vanligtvis en av följande två saker:

- Användaren har ägarbehörigheter för sin prenumeration och globala administratör på klientnivå, men de kan inte logga in på Azure.
- Användarens kontoinställningar har multifaktorautentisering aktiverad.

Så här åtgärdar du detta:

1. Kontrollera att användaren som du skapade för Azure Active Directory User Principal Name har prenumerationsnivån "Deltagare".
2. Logga in på <portal.azure.com> med UPN-kontot för att kontrollera kontoinställningarna och se till att multifaktorautentisering inte är aktiverat. Om den är påslagen stänger du av den.
3. Besök sidan Medgivande för Windows Virtual Desktop och se till att server- och klientapparna har medgivande.
4. Granska [självstudiekursen Distribuera ett hanteringsverktyg](manage-resources-using-ui.md) om problemet fortsätter och distribuera om verktyget.

## <a name="error-job-with-specified-id-already-exists"></a>Fel: Det finns redan ett jobb med angivet ID

Om användaren ser felmeddelandet "Jobb med angivet ID finns redan", beror det på att de inte angav ett unikt namn i parametern "Programnamn" när mallen distribuerades.

Åtgärda detta genom att distribuera om hanteringsverktyget med parametern "Programnamn" ifylld.

## <a name="delayed-consent-prompt-when-opening-management-tool"></a>Fördröjd samtyckesfråga när hanteringsverktyg öppnas

När du distribuerar hanteringsverktyget kanske samtyckesprompten inte öppnas direkt. Det innebär att Azure Web App-tjänsten tar längre tid än vanligt att läsa in. Prompten ska visas när Azure Web har lästs in.

## <a name="the-user-cant-deploy-the-management-tool-in-the-east-us-region"></a>Användaren kan inte distribuera hanteringsverktyget i regionen östra USA

Om en kund ställer in regionen till östra USA kan de inte distribuera hanteringsverktyget.

Lös detta genom att distribuera hanteringsverktyget i en annan region. Omfördelning av verktyget i en annan region bör inte påverka användarupplevelsen.

## <a name="next-steps"></a>Nästa steg

- Läs mer om eskaleringsspår vid [felsökningsöversikt, feedback och support](troubleshoot-set-up-overview.md).
- Lär dig hur du rapporterar problem med Windows Virtual Desktop-verktyg på [ARM-mallar för fjärrskrivbordstjänster](https://github.com/Azure/RDS-Templates/blob/master/README.md).
- En översikt över felsökning av Windows Virtual Desktop och eskaleringsspåren finns i [Felsökningsöversikt, feedback och support](troubleshoot-set-up-overview.md).
- Mer information om hur du distribuerar hanteringsverktyget finns i [Distribuera ett hanteringsverktyg](manage-resources-using-ui.md).