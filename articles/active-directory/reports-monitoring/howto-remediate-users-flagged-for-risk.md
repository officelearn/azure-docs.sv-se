---
title: Användare som flaggats för risker i Azure Active Directory-portalen | Microsoft-dokument
description: Lär dig mer om användare som flaggats i säkerhetsrapporten i Azure Active Directory-portalen
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30d02c5484ea4cce2953eac6b1b7b26a17c142bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68989705"
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>Åtgärda användare som flaggats för risk i Azure Active Directory-portalen

Med säkerhetsrapporterna i Azure Active Directory (Azure AD) kan du mäta sannolikheten för komprometterade användarkonton i din miljö. En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats.

Det är viktigt för Microsoft att hålla dina miljöer säkra. Som en del av detta åtagande övervakar Microsoft kontinuerligt för att hitta aktiviteter som är ovanliga eller som följer kända angreppsmönster. 

Om ovanliga aktiviteter som kan tyda på obehörig åtkomst till vissa av användarnas konton upptäcks får du meddelanden som gör att du kan vidta åtgärder. Detta betyder inte att Microsofts egna system har äventyrats.

## <a name="access-the-users-flagged-for-risk-report"></a>Åtkomst till användare som har flaggats för risk

Du kan granska användare som flaggats för risker via rapporten användare i [riskzonen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RiskyUsers) i Azure-portalen. Om du inte har Azure AD kan du [https://aka.ms/AccessAAD](https://aka.ms/AccessAAD)registrera dig gratis på . 

Från de användare som flaggats för riskrapport kan du vidta följande åtgärder för varje användare:

- skapa ett tillfälligt lösenord
- kräva att användarna måste återställa sina lösenord på ett säkert sätt nästa gång de loggar in
- minimera användarrisken utan att behöva vidta någon åtgärd.

Mer information finns i [Användare som flaggats för risksäkerhetsrapport](concept-user-at-risk.md).

### <a name="azure-ad-subscription-for-office-365-customers"></a>Azure AD-prenumeration för Office 365-kunder

Du kan också använda dina Office 365-autentiseringsuppgifter för att komma åt **Azure Admin Center**. När du har aktiverat din åtkomst till Azure AD omdirigeras du till Azure AD-portalen. Lägg märke till att mängden detaljer som tillhandahålls i rapporterna är begränsade på Basic-nivån. Ytterligare data och analyser är tillgängliga för Azure Premium-prenumeranter.

Så här kommer du åt **användarna som flaggats för riskrapporter** i microsoft 365-administrationscentret:

1.  Välj **Administrationscenter**på navigeringsmenyn till vänster . 
2.  Välj **Azure AD**.
3.  Logga in på **Azure Active Directory-administratörcentret**.
4.  Om en banderoll visas högst upp på sidan där det står **Checka ut den nya portalen**väljer du länken.
4.  Välj **Azure Active Directory**på navigeringsmenyn till vänster . 
5.  Välj Användare som **flaggats för risker** i avsnittet **Säkerhet** i navigeringsfönstret.

## <a name="remediation-actions"></a>Åtgärder

Gör följande för att åtgärda de berörda kontona och göra miljön säker:

1.  [Verifiera korrekt information](https://aka.ms/MFAValid) för multifaktorautentisering och återställning av lösenord för självbetjäning. 
2.  [Aktivera multifaktorautentisering](https://aka.ms/MFAuth) för alla användare. 
3.  Använd det här [reparationsskriptet](https://aka.ms/remediate) för varje påverkat konto för att automatiskt utföra följande steg: 

    a. Återställ lösenord för att skydda kontot och döda aktiva sessioner.

    b. Ta bort postlådedelegater.

    c. Inaktivera vidarebefordran av e-post för externa domäner.

    d. Ta bort egenskapen för global vidarebefordran av e-post i brevlådan.

    e. Aktivera MFA för användarens konto.

    f. Ange att lösenordskomplexiteten för kontot ska vara högt.

    g. Aktivera granskning av postlådan.

    h. Skapa en granskningslogg som administratören kan granska.

4. Undersök din Office 365-klient och övrig IT-infrastruktur, däribland en granskning av alla klientinställningar, användarkonton och konfigurationsinställningar per användare för eventuell ändring. Leta efter indikatorer på beständighetsmetoder och på att en inkräktare kan ha skapat ett första fäste för att få tag i VPN-autentiseringsuppgifter eller få åtkomst till andra resurser i organisationen. 

5.  Som en del av din utredning, överväga om du ska meddela statliga myndigheter, inklusive brottsbekämpning.

Dessutom bör du:

- Läs och implementera den här [vägledningen om hur du tar itu med ovanliga aktiviteter](https://aka.ms/fixaccount). 
- [Aktivera granskningspipelinen](https://aka.ms/improvesecurity) så att du kan analysera aktiviteten i din klientorganisation. När granskningsarkivet är klart börjar det fyllas med aktivitetsloggar. Nu kan du också utnyttja [säkerhets- och efterlevnadscentrets sök- och undersökningsresurs](https://aka.ms/sccsearch). 
- Använd det här [skriptet för att aktivera postlådegranskning](https://aka.ms/mailboxaudit1) för alla dina konton. 
- Granska reglerna för delegeringsbehörigheter och vidarebefordran av e-post för alla brevlådor. Du kan använda det här [PowerShell-skriptet](https://aka.ms/delegateforwardrules) för at utföra den här uppgiften. 

## <a name="next-steps"></a>Nästa steg

* [Identitetsskydd för Azure Active Directory](../active-directory-identityprotection.md)
* [Användare som har flaggats för risk](concept-user-at-risk.md)
