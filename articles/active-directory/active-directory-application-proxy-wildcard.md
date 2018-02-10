---
title: Jokertecken program i Azure Active Directory application proxy | Microsoft Docs
description: "Lär dig hur du använder jokertecken program i Azure Active Directory-programproxy."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: d5450da1-9e06-4d08-8146-011c84922ab5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 28e43d48b4fa27202d58ee081a60e2fb5bfe9d99
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Jokertecken program i Azure Active Directory application proxy 

I Azure Active Directory (Azure AD), konfigurera ett stort antal lokala program kan snabbt bli svårhanterligt och introducerar onödiga risker konfigurationsfel om många av dem ha samma inställningar. Med [Azure AD Application Proxy](active-directory-application-proxy-get-started.md), du kan åtgärda problemet genom att använda jokertecken programpublicering att publicera och hantera många program på samma gång. Detta är en lösning som hjälper dig att:

-   Förenkla de administrativa omkostnaderna
-   Minska antalet möjliga konfigurationsfel
-   Ge användarna säker åtkomst till fler resurser

Den här artikeln ger dig den information du behöver konfigurera jokertecken programpublicering i din miljö.


## <a name="create-a-wildcard-application"></a>Skapa ett program med jokertecken 

Du kan skapa ett program med jokertecken (*) om du har en grupp av program med samma konfiguration. Potentiella kandidater för ett program med jokertecken är tillämpningar följande inställningar:

- Gruppen med användare som har åtkomst till dem.
- Metoden enkel inloggning
- Åtkomstprotokoll (http, https)

Du kan publicera program med jokertecken om både interna och externa URL: er finns i följande format:

> http(s)://*.\<domain\>

Till exempel: `http(s)://*.adventure-works.com`. Medan de interna och externa URL: er kan använda olika domäner som bästa praxis, måste de vara detsamma. När du publicerar programmet kan ett felmeddelande visas om en av URL: erna inte har ett jokertecken.

Om du har fler program med olika konfigurationsinställningar, måste du publicera dessa undantag som separata program att skriva över standardvärdena för jokertecknet. Program utan jokertecken alltid högre prioritet än jokertecken program. Dessa är ”” vanliga program ur konfiguration.

Skapar ett program med jokertecken är baserad på samma [publishing programflöde](application-proxy-publish-azure-portal.md) som är tillgängliga för alla andra program. Den enda skillnaden är att du inkluderar ett jokertecken i URL: er och potentiellt SSO-konfiguration.


## <a name="prerequisites"></a>Förutsättningar

### <a name="custom-domains"></a>Anpassade domäner

Medan [anpassade domäner](active-directory-application-proxy-custom-domains.md) är valfritt för alla andra program, de är en förutsättning för jokertecken program. Skapa anpassade domäner måste du:

1. Skapa en verifierad domän i Azure 
2. Ladda upp ett SSL-certifikat i PFX-format till din programproxy.

Du bör använda ett jokerteckencertifikat för att matcha det program som du planerar att skapa. Alternativt kan du också använda ett certifikat som visas endast specifika program. I det här fallet kommer endast de program som anges i certifikatet vara tillgängligt via programmet med jokertecken.

Av säkerhetsskäl, detta är ett hårda krav och vi kommer inte att använda jokertecken för program som inte kan använda en anpassad domän för den externa Webbadressen.

### <a name="dns-updates"></a>DNS-uppdateringar

När du använder anpassade domäner, måste du skapa en DNS-post med en CNAME-post för den externa Webbadressen (till exempel `*.adventure-works.com`) pekar på den externa URL: en för application proxy-slutpunkten. CNAME-post måste peka på relevanta externa URL: er för jokertecken program:

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

Bekräfta att du har konfigurerat din CNAME korrekt kan du använda [nslookup](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/nslookup) på en av målslutpunkter, till exempel `expenses.adventure-works.com`.  Svaret ska inkludera redan nämnda alias (`<Id.tenant>.runtime.msappproxy.net`).


## <a name="considerations"></a>Överväganden


### <a name="accepted-formats"></a>Godkända format

För program med jokertecken, den **Intern URL** måste formateras som `http(s)://*.<domain>`. 

![AppId](./media/active-directory-application-proxy-wildcard\22.png)


När du konfigurerar en **externa URL: en**, måste du använda följande format:`https://*.<custom domain>` 

![AppId](./media/active-directory-application-proxy-wildcard\21.png)

Andra ställen jokertecknet, flera jokertecken eller andra regex-strängar stöds inte och orsakar fel.


### <a name="excluding-applications-from-the-wildcard"></a>Utesluta program från jokertecknet

Du kan utesluta ett program från programmet med jokertecken

- Publicera programmet undantag som vanliga program 
- Aktivera jokertecken endast för specifika program via DNS-inställningarna  


Publicera ett program som vanliga program är det bästa sättet att utesluta ett program från ett jokertecken. Du bör publicera undantagna program innan jokertecken-program för att säkerställa att din undantag gäller från början. Det mest specifika programmet har alltid högre prioritet – ett program som publiceras som `budgets.finance.adventure-works.com` företräde framför programmet `*.finance.adventure-works.com`, som i sin tur företräde framför programmet `*.adventure-works.com`. 

Du kan också begränsa jokertecken fungerar endast för specifika program via DNS-hantering. Som bästa praxis bör du skapa en CNAME-post som innehåller ett jokertecken och matchar formatet för den externa Webbadressen som du har konfigurerat. Du kan i stället peka specifika URL: er till jokertecken. Till exempel i stället för `*.adventure-works.com`, peka `hr.adventure-works.com`, `expenses.adventure-works.com` och `travel.adventure-works.com individually` till `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`. 

Om du använder det här alternativet måste du också en annan CNAME-post för värdet `AppId.domain`, till exempel `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com`, även peka på samma plats. Du hittar den **AppId** på egenskapssidan program av programmet med jokertecken:

![AppId](./media/active-directory-application-proxy-wildcard\01.png)



### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>Inställningen webbsida-URL: en för panelen MyApps

Jokertecken programmet representeras med bara en panel i den [MyApps panelen](https://myapps.microsoft.com). Den här panelen är dold som standard. Visa panelen och låta användare mark på en viss sida:

1. Följ riktlinjerna för [inställningen en webbsida i URL: en](application-proxy-office365-app-launcher.md).
2. Ange **Visa program** till **SANT** på egenskapssidan för programmet.

### <a name="kerberos-constrained-delegation"></a>Kerberos-begränsad delegering

För program som använder [kerberos-begränsad delegering (KCD) som metod för enkel inloggning](active-directory-application-proxy-sso-using-kcd.md), SPN-namnet visas för SSO-metod måste också ett jokertecken. SPN kan till exempel vara: `HTTP/*.adventure-works.com`. Du måste fortfarande ha enskilda SPN konfigureras på backend-servrar (till exempel `http://expenses.adventure-works.com and HTTP/travel.adventure-works.com`).



## <a name="scenario-1-general-wildcard-application"></a>Scenario 1: Allmänt jokertecken program

I det här scenariot finns det tre olika program som du vill publicera:

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Alla tre program:

- Används av alla användare
- Använd *integrerad Windows-autentisering* 
- Ha samma egenskaper


Du kan publicera programmet jokertecken med hjälp av stegen som beskrivs i [publicera program med Azure AD Application Proxy](application-proxy-publish-azure-portal.md). Det här scenariot förutsätter:

- En klient med följande ID:`000aa000-11b1-2ccc-d333-4444eee4444e` 

- En verifierad domän kallas `adventure-works.com` har konfigurerats.

- En **CNAME** post som pekar `*.adventure-works.com` till `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` har skapats.

Efter den [beskrivs stegen](application-proxy-publish-azure-portal.md), du skapar ett nytt program proxy program i din klient. I det här exemplet är jokertecknet i följande fält:

- Intern URL:

    ![Intern URL](./media/active-directory-application-proxy-wildcard\42.png)


- Externa URL:

    ![Extern webbadress](./media/active-directory-application-proxy-wildcard\43.png)

 
- Internt program SPN: 

    ![SPN-konfiguration](./media/active-directory-application-proxy-wildcard\44.png)


Genom att publicera programmet med jokertecken, du kan nu komma åt dina tre program genom att gå till URL: er som används för att (till exempel `travel.adventure-works.com`).

Konfigurationen implementerar följande struktur:

![AppId](./media/active-directory-application-proxy-wildcard\05.png)

| Färg | Beskrivning |
| ---   | ---         |
| Blå  | Program som uttryckligen publicerade och visas på Azure-portalen. |
| Grå  | Program kan nås via det överordnade programmet. |




## <a name="scenario-2-general-wildcard-application-with-exception"></a>Scenario 2: Allmänt jokertecken program med undantaget

I det här scenariot behöver du dessutom tre Allmänt program ett annat program, `finance.adventure-works.com`, som endast ska vara tillgängliga för ekonomi division. Med den aktuella programmet strukturen skulle ekonomi-programmet nås via programmet jokertecken och på alla anställda. Om du vill ändra den här exkludera ditt program från ditt jokertecken genom att konfigurera Ekonomi som ett separat program med begränsad behörighet.



Du måste se till att det finns en CNAME-poster som pekar `finance.adventure-works.com` till programmet specifika slutpunkten, anges på sidan programproxy för programmet. I det här scenariot `finance.adventure-works.com` pekar på `https://finance-awcycles.msappproxy.net/`. 

Efter den [beskrivs stegen](application-proxy-publish-azure-portal.md), det här scenariot kräver följande inställningar:


- I den **Intern URL**, du anger **ekonomi** i stället för ett jokertecken. 

    ![Intern URL](./media/active-directory-application-proxy-wildcard\52.png)

- I den **externa URL: en**, du anger **ekonomi** i stället för ett jokertecken. 

    ![Extern webbadress](./media/active-directory-application-proxy-wildcard\53.png)

- Internt program SPN du ställer in **ekonomi** i stället för ett jokertecken.

    ![SPN-konfiguration](./media/active-directory-application-proxy-wildcard\54.png)


Den här konfigurationen implementerar följande scenario:

![Scenario](./media/active-directory-application-proxy-wildcard\09.png)

Eftersom `finance.adventure-works.com` är en mer specifik URL än `*.adventure-works.com`, företräde. Användare som navigerar till `finance.adventure-works.com` har erfarenhet som angetts i programmet ekonomi resurser. I det här fallet bara ekonomianställda har åtkomst till `finance.adventure-works.com`.

Om du har flera program som publicerats för ekonomi och har `finance.adventure-works.com` som en verifierad domän kan du publicera program för en annan jokertecken `*.finance.adventure-works.com`. Eftersom det är mer specifik än allmänna `*.adventure-works.com`, företräde om en användare kommer åt ett program i domänen ekonomi.


## <a name="next-steps"></a>Nästa steg

Mer information om:

- **Anpassade domäner**, se [arbeta med anpassade domäner i Azure AD Application Proxy](active-directory-application-proxy-custom-domains.md).

- **Publicera program**, se [publicera program med Azure AD Application Proxy](application-proxy-publish-azure-portal.md)


