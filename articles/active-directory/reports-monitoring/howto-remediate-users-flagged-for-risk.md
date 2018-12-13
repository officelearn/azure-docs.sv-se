---
title: Användare som flaggats i säkerhetsrapporten i Azure Active Directory | Microsoft Docs
description: Lär dig mer om användare som flaggats i säkerhetsrapporten i Azure Active Directory-portalen
services: active-directory
author: priyamohanram
manager: mtillman
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: e81b87a6cbaddf61492fa1fc41e66950eb7ce1d7
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53191639"
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>Åtgärda användare som flaggats för risk i Azure Active Directory-portalen

Med säkerhetsrapporter i Azure Active Directory (Azure AD) kan du mäta sannolikheten för sårbara användarkonton i din miljö. En användare som flaggats för risk är en indikator för ett användarkonto som kan ha komprometterats.

Det är viktigt för Microsoft att hålla dina miljöer säkra. Som en del av detta åtagande övervakar Microsoft kontinuerligt för att hitta aktiviteter som är ovanliga eller som följer kända angreppsmönster. 

Om ovanliga aktiviteter som kan indikera obehörig åtkomst till vissa av dina användares konton identifieras måste få du meddelanden så att du vidtar åtgärder. Detta innebär inte att Microsofts egna system har komprometterats.

## <a name="access-the-users-flagged-for-risk-report"></a>Åtkomst till användare som har flaggats för risk

Du kan granska användare som har flaggats för risk med den [användare i farozonen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RiskyUsers) i Azure-portalen. Om du inte har Azure AD kan du registrera dig kostnadsfritt på [ https://aka.ms/AccessAAD ](https://aka.ms/AccessAAD). 

Från användare som flaggats för risk, kan du utföra följande åtgärder för varje användare:

- skapa ett tillfälligt lösenord
- kräva att användarna måste återställa sina lösenord på ett säkert sätt nästa gång de loggar in
- minimera användarrisken utan att behöva vidta någon åtgärd.

Mer information finns i [användare som flaggats i säkerhetsrapporten](concept-user-at-risk.md).

### <a name="azure-ad-subscription-for-office-365-customers"></a>Azure AD-prenumeration för Office 365-kunder

Du kan också använda dina Office 365-autentiseringsuppgifter för att komma åt den **Azure Admin Center**. När du har aktiverat din åtkomst till Azure AD omdirigeras du till Azure AD-portalen. Lägg märke till att mängden detaljer som tillhandahålls i rapporterna är begränsade på Basic-nivån. Ytterligare data och analyser är tillgängliga för Azure Premium-prenumeranter.

Åtkomst till den **användare som har flaggats för risk** rapporter i Administrationscenter för Office 365:

1.  På navigeringsmenyn till vänster väljer du **administratörscentra**. 
2.  Välj **Azure AD**.
3.  Logga in på **Azure Active Directory-administratörcentret**.
4.  Om en banderoll visas högst upp på sidan med texten **Kolla in den nya portalen**, klicka på länken.
4.  På navigeringsmenyn till vänster, Välj **Azure Active Directory**. 
5.  I navigeringsfönstret väljer **användare som har flaggats för risk** från den **Security** avsnittet.

## <a name="remediation-actions"></a>Åtgärder

Gör följande för att åtgärda de berörda kontona och göra miljön säker:

1.  [Verifiera rätt information](https://aka.ms/MFAValid) för multifaktorautentisering och lösenordsåterställning via självbetjäning återställa. 
2.  [Aktivera Multi-Factor authentication](https://aka.ms/MFAuth) för alla användare. 
3.  Använd det här [reparationsskript](https://aka.ms/remediate) för alla berörda konton att automatiskt utföra följande steg: 

    a. Återställa lösenord för att skydda kontot och avsluta aktiva sessioner.

    b. Ta bort postlådedelegater.

    c. Inaktivera vidarebefordran av e-post för externa domäner.

    d. Ta bort egenskapen för global vidarebefordran av e-post i brevlådan.

    e. Aktivera MFA för användarens konto.

    f. Ange att lösenordskomplexiteten för kontot ska vara högt.

    g. Aktivera granskning av postlådan.

    h. Generera en granskningslogg för en administratör att granska.

4. Undersök din Office 365-klient och övrig IT-infrastruktur, däribland en granskning av alla klientinställningar, användarkonton och konfigurationsinställningar per användare för eventuell ändring. Leta efter indikatorer på beständighetsmetoder och på att en inkräktare kan ha skapat ett första fäste för att få tag i VPN-autentiseringsuppgifter eller få åtkomst till andra resurser i organisationen. 

5.  Som en del av din undersökning kan du överväga att om du ska meddela myndigheter, däribland polisen och rättsväsendet.

Dessutom bör du:

- Läsa och implementera detta [vägledning för att uppmärksamma ovanliga aktiviteter](https://aka.ms/fixaccount). 
- [Aktivera](https://aka.ms/improvesecurity) som hjälper dig att analysera aktivitet i din klient. När du är klar börjar granskningsarkivet fyllas i med aktivitetsloggar. Nu kan du kan också använda den [Security and Compliance Centers sökning och undersökning resource](https://aka.ms/sccsearch). 
- Använd det här [skript för att aktivera granskning av postlådan](https://aka.ms/mailboxaudit1) för alla dina konton. 
- Granska reglerna för delegeringsbehörigheter och vidarebefordran av e-post för alla brevlådor. Du kan använda det här [PowerShell-skriptet](https://aka.ms/delegateforwardrules) för at utföra den här uppgiften. 

## <a name="next-steps"></a>Nästa steg

* [Identitetsskydd för Azure Active Directory](../active-directory-identityprotection.md)
* [Användare som har flaggats för risk](concept-user-at-risk.md)
