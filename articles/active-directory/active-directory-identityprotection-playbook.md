---
title: Azure Active Directory-identitetsskydd playbook | Microsoft Docs
description: "Lär dig hur Azure AD Identity Protection gör att du kan begränsa möjligheten för en angripare som utnyttjar en komprometterad identitet eller en enhet och att skydda en identitet eller en enhet som har tidigare eller misstänks vara hotad."
services: active-directory
keywords: "Azure active directory identitetsskydd, cloud app discovery, hantera program, säkerhet, risk, risknivå, säkerhetsproblem och säkerhetsprincip"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 60836abf-f0e9-459d-b344-8e06b8341d25
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: b76d6a31dfe600a4639b830bfbbb5cacfc158dd6
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2018
---
# <a name="azure-active-directory-identity-protection-playbook"></a>Azure Active Directory-identitetsskydd playbook
Den här playbook hjälper dig att:

* Fyll i data i Identity Protection-miljö genom simulering riskhändelser och säkerhetsproblem
* Konfigurera principer för risk-baserad villkorlig åtkomst och testa effekten av dessa principer

## <a name="simulating-risk-events"></a>Simulera riskhändelser
Det här avsnittet innehåller steg för att simulera händelsetyper för följande risker:

* Inloggningar från anonyma IP-adresser (enkel)
* Inloggningar från okända platser (måttlig)
* Omöjligt att resa till ovanliga platser (svårt)

Andra riskhändelser kan simuleras på ett säkert sätt.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Inloggningar från anonyma IP-adresser
Den här typen av risk händelse identifierar användare som har loggat in från en IP-adress har identifierats som en anonym proxyserver IP-adress. Dessa proxyservrar används av personer som du vill dölja enhetens IP-adress och kan användas för skadliga åtgärder.

**Utför följande steg för att simulera en inloggning från en anonym IP**:

1. Hämta den [Tor webbläsare](https://www.torproject.org/projects/torbrowser.html.en).
2. Använd Tor Browser, navigera till [https://myapps.microsoft.com](https://myapps.microsoft.com).   
3. Ange autentiseringsuppgifter för det konto som du vill ska visas i den **inloggningar från anonyma IP-adresser** rapporten.

Logga in kommer att visas på instrumentpanelen identitetsskydd inom 5 minuter. 

### <a name="sign-ins-from-unfamiliar-locations"></a>Inloggningar från okända platser
Okända platser risk är en mekanism för realtid inloggning utvärdering som överväger tidigare inloggning platser (IP, latitud / longitud och ASN) att fastställa nya / okända platser. Systemet lagrar tidigare IP-adresser, latitud / longitud och ASN: er för en användare så att de ska vara bekant platser. Okänd anses vara en inloggning plats om platsen för inloggning inte matchar någon av de befintliga bekanta platserna.

Azure Active Directory Identity Protection:  

* har en inledande learning-period på 14 dagar då inte flaggas några nya platser som okända platser.
* ignorerar inloggningar från bekant enheter och platser som är geografiskt nära en befintlig bekant plats.

Du måste logga in från en plats och en enhet som kontot inte har loggat in från innan för att simulera okända platser. 

**Utför följande steg för att simulera en inloggning från en okänd plats**:

1. Välj ett konto som har minst en 14 dagar inloggning historik. 
2. Gör något:
   
   a. När du använder en VPN-anslutning, gå till [https://myapps.microsoft.com](https://myapps.microsoft.com) och ange autentiseringsuppgifter för det konto som du vill simulera händelsen risken för.
   
   b. Be en kollega på en annan plats att logga in med autentiseringsuppgifter (rekommenderas inte).

Logga in kommer att visas på instrumentpanelen identitetsskydd inom 5 minuter.

### <a name="impossible-travel-to-atypical-location"></a>Omöjligt att resa till ovanliga plats
Det är svårt att simulera villkoret omöjligt att resa eftersom algoritmen använder maskininlärning att filtrera ut FALSKT positiva identifieringar som omöjligt att resa från bekant enheter eller inloggningar från VPN-anslutningar som används av andra användare i katalogen. Dessutom kräver algoritmen en historik av 3 till 14 dagar för användaren innan den börjar skapa riskhändelser.

**Utför följande steg för att simulera en omöjligt att resa till ovanliga plats**:

1. Använd din standard-webbläsare, navigera till [https://myapps.microsoft.com](https://myapps.microsoft.com).  
2. Ange autentiseringsuppgifter för det konto som du vill generera en händelse med omöjliga resor risken för.
3. Ändra din användaragent. Du kan ändra användaragent i Internet Explorer från utvecklingsverktyg eller ändra din användaragent i Firefox eller Chrome med en användaragent switcher tillägg.
4. Ändra IP-adress. Du kan ändra din IP-adress med hjälp av en VPN-anslutning, Tor-tillägg eller startas en ny dator i Azure i olika datacenter.
5. Logga in på [https://myapps.microsoft.com](https://myapps.microsoft.com) använder du samma inloggningsuppgifter som förut och inom några minuter efter den föregående inloggningen.

Logga in visas i instrumentpanelen för identitetsskydd inom 2-4 timmar.<br>
På grund av den komplexa maskininlärning modeller som ingår, ökar risken för den inte får tas upp.<br> Du kanske vill replikera de här stegen för flera Azure AD-konton.

## <a name="simulating-vulnerabilities"></a>Simulera säkerhetsrisker
Säkerhetsrisker är svagheter i en Azure AD-miljö som kan utnyttjas av en felaktig aktören. För närvarande exponeras 3 typer av säkerhetsproblem i Azure AD Identity Protection som utnyttjar andra funktioner i Azure AD. Dessa problem visas på instrumentpanelen identitetsskydd automatiskt när dessa funktioner har ställts in.

* Azure AD [Multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md)
* Azure AD [Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md).
* Azure AD [Privileged Identity Management](active-directory-privileged-identity-management-configure.md). 

## <a name="user-compromise-risk"></a>Användaren har komprometterats risk
**Utför följande steg om du vill testa användare risken för angrepp**:

1. Logga in på [https://portal.azure.com](https://portal.azure.com) med globala administratörsbehörigheter för din klient.
2. Gå till **identitetsskydd**. 
3. På primära **Azure AD Identity Protection** bladet, klickar du på **inställningar**. 
4. På den **portalinställningar** bladet under **säkerhetsregler**, klickar du på **användaren risken för angrepp**. 
5. På den **logga in Risk** bladet aktivera **aktivera regeln** inaktiverat och klicka sedan på **spara** inställningar.
6. För ett givet användarkonto simulera en okänd platser eller anonym IP-händelsen för risk. Detta ska upphöja användaren risknivå för **medel**.
7. Vänta en stund och kontrollera att användarnivå för dina användare är **medel**.
8. Gå till den **portalinställningar** bladet.
9. På den **användaren risken för angrepp** bladet under **aktivera regeln**väljer **på** . 
10. Välj något av följande alternativ:
    
    a. Att blockera, Välj **medel** under **Block logga in**.
    
    b. Om du vill framtvinga ändring av säkra lösenord, Välj **medel** under **kräver Multi-Factor authentication**.
11. Klicka på **Spara**.
12. Nu kan du testa risk-baserad villkorlig åtkomst genom att logga in med hjälp av en användare med en ökad risk för nivå. Om användaren risken medel, beroende på konfigurationen av din princip för inloggningen är blockeras antingen eller du måste ändra ditt lösenord. 
    <br><br>
    ![Playbook](./media/active-directory-identityprotection-playbook/201.png "Playbook")
    <br>

## <a name="sign-in-risk"></a>Inloggningsrisk
**Om du vill testa ett tecken i risk, utför du följande steg:**

1. Logga in på [https://portal.azure.com ](https://portal.azure.com) med globala administratörsbehörigheter för din klient.
2. Gå till **identitetsskydd**.
3. På primära **Azure AD Identity Protection** bladet, klickar du på **inställningar**. 
4. På den **portalinställningar** bladet under **säkerhetsregler**, klickar du på **logga in risk**.
5. På den ** logga in Risk ** bladet väljer **på** under **aktivera regeln**. 
6. Välj något av följande alternativ:
   
   a. Om du vill blockera, Välj **medel** under **Block logga in**
   
   b. Om du vill framtvinga ändring av säkra lösenord, Välj **medel** under **kräver Multi-Factor authentication**.
7. Om du vill blockera, Välj Medium under Block inloggning.
8. Om du vill framtvinga multifaktorautentisering, Välj **medel** under **kräver Multi-Factor authentication**.
9. Klicka på **Spara**.
10. Nu kan du testa risk-baserad villkorlig åtkomst genom att simulera okända platser eller anonym IP riskerar händelser eftersom de båda **medel** riskerar händelser.


![Playbook](./media/active-directory-identityprotection-playbook/200.png "Playbook")


## <a name="see-also"></a>Se också
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

