---
title: Jokertecken-program i Azure Active Directory application proxy | Microsoft Docs
description: Lär dig hur du använder jokertecken-program i Azure Active Directory-programproxyn.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2018
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 171fd5cfd18aabaa705b3ddac042f0be590d427b
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55960147"
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Jokertecken-program i Azure Active Directory-programproxyn 

I Azure Active Directory (Azure AD), konfigurera ett stort antal lokala program kan snabbt bli svårhanterligt och introducerar onödiga risker för konfigurationsfel om många av dem kräver samma inställningar. Med [Azure AD Application Proxy](application-proxy.md), du kan lösa det här problemet genom att använda jokertecken programpublicering att publicera och hantera många program på samma gång. Detta är en lösning som hjälper dig att:

-   Förenkla de administrativa omkostnaderna
-   Minska antalet möjliga konfigurationsfel
-   Ge användarna säker åtkomst till fler resurser

Den här artikeln ger dig den information du behöver att konfigurera jokertecken programpublicering i din miljö.


## <a name="create-a-wildcard-application"></a>Skapa ett jokertecken-program 

Du kan skapa ett program med jokertecken (*) om du har en grupp av program med samma konfiguration. Möjliga kandidater för ett jokertecken-program är program som delar följande inställningar:

- Gruppen med användare som har åtkomst till dem.
- Metoden enkel inloggning
- Åtkomstprotokoll (http, https)

Du kan publicera program med jokertecken om både interna och externa URL: er finns i följande format:

> http (s) :/ / *. \<domän\>

Till exempel: `http(s)://*.adventure-works.com`. De interna och externa URL: er kan använda olika domäner som bästa praxis, måste de vara densamma. När du publicerar programmet, ser du ett fel om en av URL: erna inte har ett jokertecken.

Om du har ytterligare program med olika konfigurationsinställningar, måste du publicera de här undantagen som separata program att skriva över standardinställningar för jokertecknet. Program utan jokertecken alltid företräde framför jokertecken-program. Det här är ”” vanliga program ur konfiguration.

Skapar ett jokertecken-program är baserad på samma [publishing programflödet](application-proxy-add-on-premises-application.md) som är tillgänglig för alla andra program. Den enda skillnaden är att du inkluderar ett jokertecken i URL: er och potentiellt SSO-konfiguration.


## <a name="prerequisites"></a>Förutsättningar

### <a name="custom-domains"></a>Anpassade domäner

Medan [anpassade domäner](application-proxy-configure-custom-domain.md) är valfritt för alla andra program, de är en förutsättning för jokertecken-program. Skapa anpassade domäner måste du:

1. Skapa en verifierad domän i Azure 
2. Överför ett SSL-certifikat i PFX-format till din programproxy.

Du bör överväga att använda ett jokerteckencertifikat så att de matchar de program som du planerar att skapa. Alternativt kan du också använda ett certifikat som endast visar en lista över specifika program. I det här fallet kommer endast de program som anges i certifikatet att nås via det här jokertecken-program.

Av säkerhetsskäl är det ett hårda krav och vi kommer inte att använda jokertecken för program som inte kan använda en anpassad domän för den externa URL: en.

### <a name="dns-updates"></a>DNS-uppdateringar

När du använder anpassade domäner kan du behöva skapa en DNS-post med en CNAME-post för den externa URL: en (till exempel `*.adventure-works.com`) som pekar på den externa URL: en för application proxy-slutpunkten. CNAME-posten måste peka på de relevanta externa URL: er för jokertecken-program:

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

Bekräfta att du har konfigurerat din CNAME korrekt kan du använda [nslookup](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup) på en av målslutpunkter, till exempel `expenses.adventure-works.com`.  Ditt svar ska innehålla aliaset som redan nämnts (`<yourAADTenantId>.tenant.runtime.msappproxy.net`).


## <a name="considerations"></a>Överväganden


### <a name="accepted-formats"></a>Godkända format

För jokertecken-program, den **interna URL: en** måste formateras som `http(s)://*.<domain>`. 

![appId](./media/application-proxy-wildcard/22.png)


När du konfigurerar en **externa URL: en**, måste du använda följande format: `https://*.<custom domain>` 

![appId](./media/application-proxy-wildcard/21.png)

Andra ställen jokertecknet, flera jokertecken eller andra regex-strängar stöds inte och orsakar fel.


### <a name="excluding-applications-from-the-wildcard"></a>Exkludera program från jokertecknet

Du kan utesluta ett program från jokertecken-program genom att

- Publicera programmet undantag som vanliga program 
- Aktivera jokertecken endast för specifika program via din DNS-inställningar  


Publicera ett program som vanliga program är den bästa metoden för att utesluta ett program från ett jokertecken. Du bör publicera undantagna program innan du jokertecken-program för att säkerställa att din undantag gäller från början. Det mest specifika programmet har alltid högre prioritet – ett program som publiceras som `budgets.finance.adventure-works.com` har företräde framför programmet `*.finance.adventure-works.com`, vilket i sin tur har företräde framför programmet `*.adventure-works.com`. 

Du kan också begränsa jokertecken till fungerar endast för specifika program via din DNS-hantering. Som bästa praxis bör du skapa en CNAME-post som innehåller ett jokertecken och matchar formatet för den externa URL: en som du har konfigurerat. Men kan du i stället peka specifika URL: er för jokertecken. Till exempel i stället för `*.adventure-works.com`, peka `hr.adventure-works.com`, `expenses.adventure-works.com` och `travel.adventure-works.com individually` till `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`. 

Om du använder det här alternativet kan du även behöver en annan CNAME-posten för värdet `AppId.domain`, till exempel `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com`, också peka på samma plats. Du hittar den **AppId** på egenskapssidan för programmet för jokertecken-program:

![appId](./media/application-proxy-wildcard/01.png)



### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>Ange hemsides-URL för panelen MyApps

Jokertecken-program representeras med bara en panel i den [MyApps panelen](https://myapps.microsoft.com). Den här panelen är dold som standard. Visa panelen och har användarna mark på en viss sida:

1. Följ riktlinjerna för [ställa in en URL för startsidan](application-proxy-configure-custom-home-page.md).
2. Ange **visa programmet** till **SANT** på egenskapssidan för programmet.

### <a name="kerberos-constrained-delegation"></a>Kerberos-begränsad delegering

För program som använder [kerberos-begränsad delegering (KCD) som metod för enkel inloggning](application-proxy-configure-single-sign-on-with-kcd.md), SPN i listan för SSO-metod måste också ett jokertecken. Tjänstens Huvudnamn kan till exempel vara: `HTTP/*.adventure-works.com`. Du måste fortfarande ha enskilda SPN-namn som konfigurerats på backend-servrar (till exempel `http://expenses.adventure-works.com and HTTP/travel.adventure-works.com`).



## <a name="scenario-1-general-wildcard-application"></a>Scenario 1: Allmänna jokertecken-program

I det här scenariot har du tre olika program som du vill publicera:

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Alla tre program:

- Används av alla användare
- Använd *integrerad Windows-autentisering* 
- Har samma egenskaper


Du kan publicera med jokertecken-program genom att följa stegen i [publicera program med Azure AD Application Proxy](application-proxy-add-on-premises-application.md). Det här scenariot förutsätter:

- En klient med följande ID: `000aa000-11b1-2ccc-d333-4444eee4444e` 

- En verifierad domän kallas `adventure-works.com` har konfigurerats.

- En **CNAME** post som pekar `*.adventure-works.com` till `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` har skapats.

Efter den [beskrivs stegen](application-proxy-add-on-premises-application.md), du skapar ett nytt application proxy-program i din klient. I det här exemplet är jokertecknet i följande fält:

- Intern URL:

    ![Intern URL](./media/application-proxy-wildcard/42.png)


- Externa URL: en:

    ![Extern webbadress](./media/application-proxy-wildcard/43.png)

 
- Intern program-SPN: 

    ![SPN-konfiguration](./media/application-proxy-wildcard/44.png)


Genom att publicera programmet med jokertecken, du kan nu komma åt dina tre program genom att gå till URL: er som du är van vid (till exempel `travel.adventure-works.com`).

Konfigurationen implementerar följande struktur:

![appId](./media/application-proxy-wildcard/05.png)

| Färg | Beskrivning |
| ---   | ---         |
| Blå  | Program som uttryckligen publicerat och synliga i Azure Portal. |
| Grå  | Program du kan nås via det överordnade programmet. |




## <a name="scenario-2-general-wildcard-application-with-exception"></a>Scenario 2: Allmänna jokertecken-program med undantag

I det här scenariot behöver du dessutom tre Allmänt program ett annat program `finance.adventure-works.com`, som endast ska vara tillgängliga för ekonomi division. Med den aktuella programmet strukturen är ekonomi-program tillgänglig via jokertecken-program och av alla anställda. Om du vill ändra detta undanta ditt program från ditt jokertecken genom att konfigurera Ekonomi som ett separat program med mer restriktiva behörigheter.



Du måste se till att det finns en CNAME-poster som pekar `finance.adventure-works.com` till den specifika programslutpunkten, anges på sidan Application Proxy för programmet. Det här scenariot `finance.adventure-works.com` pekar på `https://finance-awcycles.msappproxy.net/`. 

Efter den [beskrivs stegen](application-proxy-add-on-premises-application.md), det här scenariot kräver följande inställningar:


- I den **interna URL: en**, du ställer in **ekonomi** i stället för ett jokertecken. 

    ![Intern URL](./media/application-proxy-wildcard/52.png)

- I den **externa URL: en**, du ställer in **ekonomi** i stället för ett jokertecken. 

    ![Extern webbadress](./media/application-proxy-wildcard/53.png)

- Intern program-SPN som du ställer in **ekonomi** i stället för ett jokertecken.

    ![SPN-konfiguration](./media/application-proxy-wildcard/54.png)


Den här konfigurationen implementerar följande scenario:

![Scenario](./media/application-proxy-wildcard/09.png)

Eftersom `finance.adventure-works.com` är en mer specifik URL än `*.adventure-works.com`, den företräde. Användare som navigerar till `finance.adventure-works.com` har erfarenhet som angetts i programmet ekonomi-resurser. I det här fallet bara medarbetare ska kunna komma åt `finance.adventure-works.com`.

Om du har flera program som publicerats för ekonomi och du har `finance.adventure-works.com` som en verifierad domän kan du publicera ett annat program för jokertecken `*.finance.adventure-works.com`. Eftersom det här är mer specifik än allmän `*.adventure-works.com`, den företräde om en användare ansluter till ett program i ekonomi-domän.


## <a name="next-steps"></a>Nästa steg

Mer information om:

- **Anpassade domäner**, se [arbeta med anpassade domäner i Azure AD Application Proxy](application-proxy-configure-custom-domain.md).

- **Publicera program**, se [publicera program med Azure AD Application Proxy](application-proxy-add-on-premises-application.md)


