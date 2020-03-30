---
title: Åtgärda problem med dynamiska gruppmedlemskap – Azure AD | Microsoft-dokument
description: Felsökningstips för dynamiskt gruppmedlemskap i Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f685ac63e3b4a8cf466be4eb4561472fb084d49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74026546"
---
# <a name="troubleshoot-and-resolve-groups-issues"></a>Felsöka och lösa gruppproblem

## <a name="troubleshooting-group-creation-issues"></a>Felsöka problem med att skapa grupper

**Jag har inaktiverat skapandet av säkerhetsgrupper i Azure-portalen men grupper kan fortfarande skapas via Powershell** **Användaren kan skapa säkerhetsgrupper i Azure-portaler** i Azure-portalen kontroller oavsett om icke-admin användare kan skapa säkerhetsgrupper i Access-panelen eller Azure-portalen. Det styr inte skapandet av säkerhetsgrupper via Powershell.

Så här inaktiverar du gruppskapande för användare som inte är administratörer i Powershell:
1. Kontrollera att användare som inte är administratörer får skapa grupper:
   

   ```powershell
   Get-MsolCompanyInformation | Format-List UsersPermissionToCreateGroupsEnabled
   ```

  
2. Om den `UsersPermissionToCreateGroupsEnabled : True`returneras kan användare som inte är administratörer skapa grupper. Så här inaktiverar du den här funktionen:
  

   ``` 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```

<br/>**Jag fick ett max-antal tillåtna fel när jag försökte skapa en dynamisk grupp i Powershell**<br/>
Om du får ett meddelande i Powershell som anger _att dynamiska grupprinciper max tillåtna grupper har nåtts_innebär det att du har nått maxgränsen för dynamiska grupper i din klientorganisation. Det maximala antalet dynamiska grupper per klient är 5 000.

Om du vill skapa nya dynamiska grupper måste du först ta bort några befintliga dynamiska grupper. Det finns inget sätt att höja gränsen.

## <a name="troubleshooting-dynamic-memberships-for-groups"></a>Felsöka dynamiska medlemskap för grupper

**Jag har konfigurerat en regel för en grupp men inga medlemskap uppdateras i gruppen**<br/>
1. Verifiera värdena för användar- eller enhetsattribut i regeln. Se till att det finns användare som uppfyller regeln. För enheter kontrollerar du enhetens egenskaper för att säkerställa att alla synkroniserade attribut innehåller de förväntade värdena.<br/>
2. Kontrollera status för medlemskapsbearbetning för att bekräfta om den är klar. Du kan kontrollera [statusen för medlemskapsbearbetning](groups-create-rule.md#check-processing-status-for-a-rule) och det senast uppdaterade datumet på sidan **Översikt** för gruppen.

Om allt ser bra ut, låt lite tid för gruppen att fylla. Beroende på klientens storlek så kan det ta upp till 24 timmar att fylla gruppen första gången eller efter en regeländring.

**Jag har konfigurerat en regel, men nu tas de befintliga medlemmarna i regeln bort**<br/>Det här beteendet är förväntat. Befintliga medlemmar i gruppen tas bort när en regel är aktiverad eller ändrad. De användare som returneras från utvärderingen av regeln läggs till som medlemmar i gruppen.

**Jag ser inte medlemskapsändringar direkt när jag lägger till eller ändrar en regel, varför inte?**<br/>Dedikerad medlemskapsutvärdering görs regelbundet i en asynkron bakgrundsprocess. Hur lång tid processen tar bestäms av antalet användare i katalogen och storleken på den grupp som skapats som ett resultat av regeln. Vanligtvis ser kataloger med ett litet antal användare gruppmedlemskapet på mindre än några minuter. Kataloger med ett stort antal användare kan ta 30 minuter eller längre att fylla.

**Hur kan jag tvinga gruppen att behandlas nu?**<br/>
För närvarande finns det inget sätt att automatiskt utlösa den grupp som ska bearbetas på begäran. Du kan dock manuellt utlösa upparbetningen genom att uppdatera medlemskapsregeln för att lägga till ett blanktecken i slutet.  

**Jag påträffade ett fel vid regelbearbetning**<br/>I följande tabell visas vanliga dynamiska medlemsregelfel och hur du korrigerar dem.

| Fel vid regeltolkare | Felanvändning | Korrigerad användning |
| --- | --- | --- |
| Fel: Attributet stöds inte. |(user.invalidProperty -eq "Värde") |(user.avdelning -eq "värde")<br/><br/>Kontrollera att attributet finns i [egenskapslistan som stöds](groups-dynamic-membership.md#supported-properties). |
| Fel: Operatören stöds inte på attributet. |(user.accountEnabled -innehåller sant) |(user.accountEnabled -eq sant)<br/><br/>Operatorn som används stöds inte för egenskapstypen (i det här exemplet innehåller -innehåller inte kan användas på typen boolean). Använd rätt operatorer för egenskapstypen. |
| Fel: Frågekompileringsfel. | 1. (user.department -eq "Försäljning") (user.department -eq "Marknadsföring")<br>2. (user.userPrincipalName -match@domain.ext"* ") | 1. Operatör saknas. Använd -och eller -eller två kopplingspredikater<br>(user.department -eq "Försäljning") -eller (user.department -eq "Marknadsföring")<br>2. Fel i reguljärt uttryck som används med -match<br>(user.userPrincipalName -match ".*@domain.ext")<br>eller alternativt: (user.userPrincipalName -match "@domain.ext$") |

## <a name="next-steps"></a>Nästa steg

Dessa artiklar innehåller ytterligare information om Azure Active Directory.

* [Hantera åtkomst till resurser med Azure Active Directory-grupper](../fundamentals/active-directory-manage-groups.md)
* [Programhantering i Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Vad är Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](../hybrid/whatis-hybrid-identity.md)