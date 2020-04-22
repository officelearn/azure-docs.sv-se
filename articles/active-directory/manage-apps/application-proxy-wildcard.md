---
title: Jokerteckenprogram i Azure AD-programproxy
description: Lär dig hur du använder Jokerteckenprogram i Azure Active Directory-programproxyn.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2018
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e5861e802f39adecb5661bc17c22b432f137d59
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770305"
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Jokerteckenprogram i Azure Active Directory-programproxyn

I Azure Active Directory (Azure AD) kan konfigurera ett stort antal lokala program snabbt bli ohanterliga och medför onödiga risker för konfigurationsfel om många av dem kräver samma inställningar. Med [Azure AD Application Proxy](application-proxy.md)kan du lösa problemet genom att använda wildcard-programpublicering för att publicera och hantera många program samtidigt. Detta är en lösning som gör att du kan:

- Förenkla dina administrativa kostnader
- Minska antalet potentiella konfigurationsfel
- Gör det möjligt för användarna att på ett säkert sätt komma åt fler resurser

Den här artikeln innehåller den information du behöver för att konfigurera publicering av jokerteckenprogram i din miljö.

## <a name="create-a-wildcard-application"></a>Skapa ett jokerteckenprogram

Du kan skapa ett jokerteckenprogram (*) om du har en grupp program med samma konfiguration. Potentiella kandidater för ett jokerteckenprogram är program som delar följande inställningar:

- Den grupp användare som har tillgång till dem
- SSO-metoden
- Åtkomstprotokollet (http, https)

Du kan publicera program med jokertecken om båda, de interna och externa webbadresserna är i följande format:

> http(s)://*. \<domän\>

Till exempel: `http(s)://*.adventure-works.com`.

Även om interna och externa webbadresser kan använda olika domäner bör de som bästa praxis vara desamma. När du publicerar programmet visas ett fel om en av webbadresserna inte har ett jokertecken.

Att skapa ett jokerteckenprogram baseras på samma [programpubliceringsflöde](application-proxy-add-on-premises-application.md) som är tillgängligt för alla andra program. Den enda skillnaden är att du inkluderar ett jokertecken i webbadresserna och eventuellt SSO-konfigurationen.

## <a name="prerequisites"></a>Krav

För att komma igång, se till att du har uppfyllt dessa krav.

### <a name="custom-domains"></a>Anpassade domäner

Även [om anpassade domäner](application-proxy-configure-custom-domain.md) är valfria för alla andra program, är de en förutsättning för jokerteckenprogram. För att skapa anpassade domäner måste du:

1. Skapa en verifierad domän i Azure.
1. Ladda upp ett TLS/SSL-certifikat i PFX-format till din programproxy.

Du bör överväga att använda ett jokerteckencertifikat för att matcha det program du planerar att skapa. Du kan också använda ett certifikat som bara listar specifika program. I det här fallet är endast de program som anges i certifikatet tillgängliga via det här jokertecknet.

Av säkerhetsskäl är detta ett svårt krav och vi kommer inte att stödja jokertecken för program som inte kan använda en anpassad domän för den externa URL:en.

### <a name="dns-updates"></a>DNS-uppdateringar

När du använder anpassade domäner måste du skapa en DNS-post med en `*.adventure-works.com`CNAME-post för den externa URL:en (till exempel) som pekar på den externa URL:en för programproxyslutpunkten. För jokerteckenprogram måste CNAME-posten peka på relevanta externa webbadresser:

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

Om du vill bekräfta att du har konfigurerat CNAME korrekt kan du använda [nslookup](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup) på en av målslutpunkterna, till exempel `expenses.adventure-works.com`.  Ditt svar bör innehålla det`<yourAADTenantId>.tenant.runtime.msappproxy.net`redan nämnda aliaset ( ).

## <a name="considerations"></a>Överväganden

Här är några överväganden som du bör ta hänsyn till för jokertecken applikationer.

### <a name="accepted-formats"></a>Godkända format

För jokerteckenprogram måste den **interna URL:en** formateras som `http(s)://*.<domain>`.

![För intern WEBBADRESS använder du formatet http/s)://*. \<domän>](./media/application-proxy-wildcard/22.png)

När du konfigurerar en **extern URL**måste du använda följande format:`https://*.<custom domain>`

![För extern URL använder du formatet https://*. \<anpassad domän>](./media/application-proxy-wildcard/21.png)

Andra positioner för jokertecken, flera jokertecken eller andra regexsträngar stöds inte och orsakar fel.

### <a name="excluding-applications-from-the-wildcard"></a>Exklusive ansökningar från jokertecken

Du kan utesluta ett program från jokerteckenprogrammet genom att

- Publicera undantagsprogrammet som vanligt program
- Aktivera jokertecknet endast för specifika program via DNS-inställningarna

Att publicera ett program som vanligt program är den metod som föredras för att utesluta ett program från ett jokertecken. Du bör publicera de uteslutna programmen före jokerteckenprogrammen för att säkerställa att dina undantag tillämpas från början. Det mest specifika programmet har alltid företräde `budgets.finance.adventure-works.com` – ett program `*.finance.adventure-works.com`som publiceras som har företräde `*.adventure-works.com`framför programmet , som i sin tur har företräde framför programmet .

Du kan också begränsa jokertecknet så att det bara fungerar för specifika program via DNS-hanteringen. Du bör också skapa en CNAME-post som innehåller ett jokertecken och matchar formatet för den externa URL som du har konfigurerat. Du kan dock i stället peka specifika programadresser till jokertecken. I `*.adventure-works.com`stället för , `hr.adventure-works.com` `expenses.adventure-works.com` peka `travel.adventure-works.com individually` `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`och till .

Om du använder det här alternativet behöver du `AppId.domain`också en `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com`annan CNAME-post för värdet, till exempel , som också pekar på samma plats. Du hittar **AppId** på sidan programegenskaper i jokerteckenprogrammet:

![Hitta program-ID:t på appens egenskapssida](./media/application-proxy-wildcard/01.png)

### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>Ställa in url:en för startsidan för panelen Mina MinaPpor

Jokerteckenprogrammet representeras med bara en bricka på [panelen MyApps](https://myapps.microsoft.com). Som standard är den här panelen dold. Så här visar du panelen och låter användarna landa på en viss sida:

1. Följ riktlinjerna för [att ange en webbadress](application-proxy-configure-custom-home-page.md).
1. Ange **Visa program** till **true** på sidan programegenskaper.

### <a name="kerberos-constrained-delegation"></a>Kerberos begränsad delegering

För program som använder [Kerberos-begränsad delegering (KCD) som SSO-metod](application-proxy-configure-single-sign-on-with-kcd.md)kan SPN som anges för SSO-metoden också behöva ett jokertecken. Spn kan till exempel `HTTP/*.adventure-works.com`vara: . Du måste fortfarande ha de enskilda SPN-nätverken konfigurerade `HTTP/expenses.adventure-works.com and HTTP/travel.adventure-works.com`på serveringsservrarna (till exempel).

## <a name="scenario-1-general-wildcard-application"></a>Scenario 1: Allmänt jokerteckenprogram

I det här fallet har du tre olika program som du vill publicera:

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Alla tre ansökningar:

- Används av alla dina användare
- Använda *integrerad Windows-autentisering*
- Har samma egenskaper

Du kan publicera jokerteckenprogrammet med hjälp av stegen i [Publicera program med Azure AD Application Proxy](application-proxy-add-on-premises-application.md). Det här scenariot förutsätter:

- En klient med följande ID:`000aa000-11b1-2ccc-d333-4444eee4444e`
- En verifierad `adventure-works.com` domän som anropas har konfigurerats.
- En **CNAME-post** `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` som pekar på `*.adventure-works.com` har skapats.

I de [dokumenterade stegen](application-proxy-add-on-premises-application.md)skapar du ett nytt programproxyprogram i klienten. I det här exemplet finns jokertecknet i följande fält:

- Intern URL:

    ![Exempel: Jokertecken i intern URL](./media/application-proxy-wildcard/42.png)

- Extern URL:

    ![Exempel: Jokertecken i extern URL](./media/application-proxy-wildcard/43.png)

- Spn för intern tillämpning:

    ![Exempel: Jokertecken i SPN-konfiguration](./media/application-proxy-wildcard/44.png)

Genom att publicera jokerteckenprogrammet kan du nu komma åt dina tre program genom att `travel.adventure-works.com`navigera till de webbadresser som du är van vid (till exempel ).

Konfigurationen implementerar följande struktur:

![Visar den struktur som implementeras av exempelkonfigurationen](./media/application-proxy-wildcard/05.png)

| Color | Beskrivning |
| ---   | ---         |
| Blå  | Program som uttryckligen publicerats och visas i Azure-portalen. |
| Grå  | Program som du kan få direkt tillgänglig via det överordnade programmet. |

## <a name="scenario-2-general-wildcard-application-with-exception"></a>Scenario 2: Allmänt jokerteckenprogram med undantag

I det här fallet har du förutom de `finance.adventure-works.com`tre allmänna programmen ett annat program, som endast bör vara tillgänglig för Finance-divisionen. Med den aktuella programstrukturen skulle ditt ekonomiprogram vara tillgängligt via jokerteckenprogrammet och av alla anställda. Om du vill ändra detta utesluter du ditt program från jokertecknet genom att konfigurera Finance som ett separat program med mer restriktiva behörigheter.

Du måste se till att det finns `finance.adventure-works.com` en CNAME-poster som pekar på den programspecifika slutpunkten, som anges på sidan Programproxy för programmet. I det `finance.adventure-works.com` här `https://finance-awcycles.msappproxy.net/`scenariot pekar du på .

I de [dokumenterade stegen](application-proxy-add-on-premises-application.md)kräver det här scenariot följande inställningar:

- I den **interna webbadressen**anger du **finansiering** i stället för ett jokertecken.

    ![Exempel: Ange finansiering i stället för ett jokertecken i intern URL](./media/application-proxy-wildcard/52.png)

- I den **externa URL:en**anger du **finansiering** i stället för ett jokertecken.

    ![Exempel: Ange finansiering i stället för ett jokertecken i extern URL](./media/application-proxy-wildcard/53.png)

- Internt program SPN du anger **finansiering** i stället för ett jokertecken.

    ![Exempel: Ange finansiering i stället för ett jokertecken i SPN-konfiguration](./media/application-proxy-wildcard/54.png)

Den här konfigurationen implementerar följande scenario:

![Visar konfigurationen som implementerats av exempelscenariot](./media/application-proxy-wildcard/09.png)

Eftersom `finance.adventure-works.com` det är en `*.adventure-works.com`mer specifik URL än har den företräde. Användare som `finance.adventure-works.com` navigerar för att få den upplevelse som anges i programmet Ekonomiresurser. I det här fallet kan endast `finance.adventure-works.com`ekonomianställda komma åt .

Om du har publicerat flera program `finance.adventure-works.com` för finansiering och du har som `*.finance.adventure-works.com`verifierad domän kan du publicera ett annat jokerteckenprogram . Eftersom detta är mer `*.adventure-works.com`specifikt än det allmänna har den företräde om en användare har åtkomst till ett program i ekonomidomänen.

## <a name="next-steps"></a>Nästa steg

- Mer information om **anpassade domäner**finns [i Arbeta med anpassade domäner i Azure AD Application Proxy](application-proxy-configure-custom-domain.md).
- Mer information om **publiceringsprogram**finns i [Publicera program med Azure AD Application Proxy](application-proxy-add-on-premises-application.md)
