---
title: Åtgärda problem för dynamiskt medlemskap för grupper – Azure Active Directory | Microsoft Docs
description: Felsökningstips för dynamiskt medlemskap för grupper i Azure AD.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0eededcc180d7652fd52c79b85ca3c34f65a22a4
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58791566"
---
# <a name="troubleshoot-and-resolve-groups-issues"></a>Felsöka och lösa problem

## <a name="troubleshooting-group-creation-issues"></a>Felsökning av problem med skapande av grupp

**Jag har inaktiverat security skapas i Azure-portalen men kan fortfarande skapas via Powershell** den **användare kan skapa säkerhetsgrupper i Azure-portaler** i Azure portal-kontrollerna huruvida icke-administratörer användare kan skapa säkerhetsgrupper i åtkomstpanelen eller Azure-portalen. Det styr inte security skapas via Powershell.

Inaktivera skapande av en grupp för icke-administratörer i Powershell:
1. Kontrollera att icke-administratörer har behörighet att skapa grupper:
   

   ```powershell
   Get-MsolCompanyInformation | Format-List UsersPermissionToCreateGroupsEnabled
   ```

  
2. Om den returnerar `UsersPermissionToCreateGroupsEnabled : True`, icke-administratörer kan skapa grupper. Inaktivera den här funktionen:
  

   ``` 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```

<br/>**Jag har fått en max grupper tillåtna fel vid försök att skapa en dynamisk grupp i Powershell**<br/>
Om du får ett meddelande i Powershell som anger _dynamiska grupprinciper max tillåtna grupper antalet har nåtts_, det innebär att du har nått maxgränsen för dynamiska grupper i din klient. Max antal dynamiska grupper per klient är 5 000.

För att skapa några nya dynamiska grupper, behöver du först ta bort några befintliga dynamiska grupper. Det går inte att öka gränsen.

## <a name="troubleshooting-dynamic-memberships-for-groups"></a>Felsöka dynamiska medlemskap för grupper

**Jag har konfigurerat en regel för en grupp, men inget medlemskap uppdateras i gruppen**<br/>
1. Kontrollera värdena för användaren eller enhetens egenskaper i regeln. Se till att det finns användare som uppfyller regeln. Kontrollera egenskaperna för enheten för att säkerställa att alla synkroniserade attributen innehåller de förväntade värdena för enheter.<br/>
2. Kontrollera medlemskapet för Bearbetningsstatus för att bekräfta om den är klar. Du kan kontrollera den [medlemskap Bearbetningsstatus](groups-create-rule.md#check-processing-status-for-a-rule) och senaste uppdaterade datum på den **översikt** för gruppen.

Om allt ser bra ut, det kan ta lite tid att fylla i gruppen. Beroende på klientens storlek så kan det ta upp till 24 timmar att fylla gruppen första gången eller efter en regeländring.

**Jag har konfigurerat en regel, men nu de befintliga medlemmarna i regeln tas bort**<br/>Detta är förväntat. Befintliga medlemmar i gruppen tas bort när en regel är aktiverat eller ändrats. De användare som har returnerats från utvärdering av regeln läggs till som medlemmar i gruppen.

**Jag ser inte medlemskap ändras direkt när jag lägger till eller ändra en regel, varför inte?**<br/>Dedikerad medlemskapsutvärdering sker regelbundet i en asynkron bakgrundsprocess. Hur lång tid processen tar bestäms av antalet användare i din katalog och storleken på gruppen skapas till följd av regeln. Normalt ser kataloger med ett litet antal användare ändringarna av gruppmedlemskap i mindre än ett par minuter. Kataloger med ett stort antal användare kan ta 30 minuter eller längre tid att fylla i.

**Hur kan jag tvinga gruppen som ska bearbetas nu?**<br/>
Det finns för närvarande inget sätt att automatiskt utlösa gruppen som ska bearbetas på begäran. Du kan dock manuellt utlösa när denna ombearbetning genom att uppdatera medlemskapsregeln för att lägga till ett blanksteg i slutet.  

**Jag påträffade ett fel vid bearbetning av regel**<br/>I följande tabell visas vanliga dynamiskt medlemskap regeln fel och hur du åtgärdar dem.

| Regeln parsningsfel | Fel vid användning | Korrigerad användning |
| --- | --- | --- |
| Fel: Attributet stöds inte. |(user.invalidProperty - eq ”Value”) |(user.department - eq ”value”)<br/><br/>Kontrollera att attributet är på den [stöds egenskapslistan](groups-dynamic-membership.md#supported-properties). |
| Fel: Operatorn stöds inte på attribut. |(user.accountEnabled-innehåller SANT) |(user.accountEnabled -eq true)<br/><br/>Den operator som används stöds inte för egenskapstypen (i det här exemplet-innehåller kan inte användas på Skriv booleskt värde). Använd rätt operatorer för egenskapstypen. |
| Fel: Frågekompileringsfel. | 1. (user.department -eq "Sales") (user.department -eq "Marketing")<br>2.  (user.userPrincipalName -match "*@domain.ext") | 1. Operator saknas. Använd - och eller - eller två gå med i predikat<br>(user.department -eq "Sales") -or (user.department -eq "Marketing")<br>2. Fel i reguljärt uttryck som används med - matcha<br>(user.userPrincipalName -match ".*@domain.ext")<br>eller alternativt: (user.userPrincipalName-matchar ”@domain.ext$”) |

## <a name="next-steps"></a>Nästa steg

Dessa artiklar innehåller ytterligare information om Azure Active Directory.

* [Hantera åtkomst till resurser med Azure Active Directory-grupper](../fundamentals/active-directory-manage-groups.md)
* [Programhantering i Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Vad är Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](../hybrid/whatis-hybrid-identity.md)