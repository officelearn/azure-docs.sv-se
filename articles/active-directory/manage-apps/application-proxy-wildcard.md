---
title: Jokertecken i Azure-AD-programproxy
description: Lär dig hur du använder jokertecken-program i Azure Active Directory-programproxyn.
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
ms.openlocfilehash: c5a9e7be5f582051e03cba08733fcbfa697cc8f5
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275036"
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Jokertecken-program i Azure Active Directory-programproxyn

I Azure Active Directory (Azure AD), konfigurera ett stort antal lokala program kan snabbt bli svårhanterligt och introducerar onödiga risker för konfigurationsfel om många av dem kräver samma inställningar. Med [Azure AD-programproxy](application-proxy.md)kan du lösa det här problemet genom att använda program publicering med jokertecken för att publicera och hantera många program samtidigt. Detta är en lösning som hjälper dig att:

- Förenkla de administrativa omkostnaderna
- Minska antalet möjliga konfigurationsfel
- Ge användarna säker åtkomst till fler resurser

Den här artikeln ger dig den information du behöver att konfigurera jokertecken programpublicering i din miljö.

## <a name="create-a-wildcard-application"></a>Skapa ett jokertecken-program

Du kan skapa ett program med jokertecken (*) om du har en grupp av program med samma konfiguration. Möjliga kandidater för ett jokertecken-program är program som delar följande inställningar:

- Gruppen med användare som har åtkomst till dem.
- Metoden enkel inloggning
- Åtkomstprotokoll (http, https)

Du kan publicera program med jokertecken om både interna och externa URL: er finns i följande format:

> http (s)://*.\<domän\>

Till exempel: `http(s)://*.adventure-works.com`.

De interna och externa URL: er kan använda olika domäner som bästa praxis, måste de vara densamma. När du publicerar programmet, ser du ett fel om en av URL: erna inte har ett jokertecken.

Om du har ytterligare program med olika konfigurationsinställningar, måste du publicera de här undantagen som separata program att skriva över standardinställningar för jokertecknet. Program utan jokertecken alltid företräde framför jokertecken-program. Det här är ”” vanliga program ur konfiguration.

Att skapa ett program med jokertecken baseras på samma [program publicerings flöde](application-proxy-add-on-premises-application.md) som är tillgängligt för alla andra program. Den enda skillnaden är att du inkluderar ett jokertecken i URL: er och potentiellt SSO-konfiguration.

## <a name="prerequisites"></a>Krav

Kom igång genom att se till att du uppfyller dessa krav.

### <a name="custom-domains"></a>Anpassade domäner

[Anpassade domäner](application-proxy-configure-custom-domain.md) är valfria för alla andra program, men de är en förutsättning för program med jokertecken. Skapa anpassade domäner måste du:

1. Skapa en verifierad domän i Azure.
1. Överför ett SSL-certifikat i PFX-format till din programproxy.

Du bör överväga att använda ett jokerteckencertifikat så att de matchar de program som du planerar att skapa. Alternativt kan du också använda ett certifikat som endast visar en lista över specifika program. I det här fallet kommer endast de program som anges i certifikatet att nås via det här jokertecken-program.

Av säkerhetsskäl är det ett hårda krav och vi kommer inte att använda jokertecken för program som inte kan använda en anpassad domän för den externa URL: en.

### <a name="dns-updates"></a>DNS-uppdateringar

När du använder anpassade domäner måste du skapa en DNS-post med en CNAME-post för den externa URL: en (till exempel `*.adventure-works.com`) som pekar på den externa URL: en för Application Proxy-slutpunkten. För program med jokertecken måste CNAME-posten peka på relevanta externa URL: er:

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

För att bekräfta att du har konfigurerat din CNAME korrekt kan du använda [nslookup](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup) på en av mål slut punkterna, till exempel `expenses.adventure-works.com`.  Ditt svar bör innehålla det alias som redan nämnts (`<yourAADTenantId>.tenant.runtime.msappproxy.net`).

## <a name="considerations"></a>Överväganden

Här följer några överväganden som du bör tänka på när du använder jokertecken.

### <a name="accepted-formats"></a>Godkända format

För jokertecken måste den **interna URL:** en vara formaterad som `http(s)://*.<domain>`.

![Använd formatet http (s)://* för intern URL.\<domän >](./media/application-proxy-wildcard/22.png)

När du konfigurerar en **extern URL**måste du använda följande format: `https://*.<custom domain>`

![För extern URL använder du formatet https://*.\<anpassad domän >](./media/application-proxy-wildcard/21.png)

Andra ställen jokertecknet, flera jokertecken eller andra regex-strängar stöds inte och orsakar fel.

### <a name="excluding-applications-from-the-wildcard"></a>Exkludera program från jokertecknet

Du kan utesluta ett program från jokertecken-program genom att

- Publicera programmet undantag som vanliga program
- Aktivera jokertecken endast för specifika program via din DNS-inställningar

Publicera ett program som vanliga program är den bästa metoden för att utesluta ett program från ett jokertecken. Du bör publicera undantagna program innan du jokertecken-program för att säkerställa att din undantag gäller från början. Det mest specifika programmet prioriteras alltid – ett program som publiceras som `budgets.finance.adventure-works.com` har företräde framför programmet `*.finance.adventure-works.com`, vilket i sin tur prioriteras över programmet `*.adventure-works.com`.

Du kan också begränsa jokertecken till fungerar endast för specifika program via din DNS-hantering. Som bästa praxis bör du skapa en CNAME-post som innehåller ett jokertecken och matchar formatet för den externa URL: en som du har konfigurerat. Men kan du i stället peka specifika URL: er för jokertecken. Till exempel, i stället för `*.adventure-works.com`, peka `hr.adventure-works.com`, `expenses.adventure-works.com` och `travel.adventure-works.com individually` till `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`.

Om du använder det här alternativet behöver du också en annan CNAME-post för värdet `AppId.domain`, till exempel `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com`, som också pekar på samma plats. Du kan hitta **AppId** på sidan program egenskaper i programmet jokertecken:

![Hitta program-ID: t på appens egenskaps sida](./media/application-proxy-wildcard/01.png)

### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>Ange hemsides-URL för panelen MyApps

Programmet med jokertecken representeras bara av en panel i panelen för mina [appar](https://myapps.microsoft.com). Den här panelen är dold som standard. Visa panelen och har användarna mark på en viss sida:

1. Följ rikt linjerna för att [ställa in en URL för start sidan](application-proxy-configure-custom-home-page.md).
1. Ange **Visa program** till **True** på sidan program egenskaper.

### <a name="kerberos-constrained-delegation"></a>Kerberos-begränsad delegering

För program [som använder Kerberos-begränsad delegering (KCD) som SSO-metod](application-proxy-configure-single-sign-on-with-kcd.md)kan SPN-namnet som anges för SSO-metoden också ha ett jokertecken. SPN kan till exempel vara: `HTTP/*.adventure-works.com`. Du måste fortfarande ha de enskilda SPN som kon figurer ATS på backend-servrarna (till exempel `http://expenses.adventure-works.com and HTTP/travel.adventure-works.com`).

## <a name="scenario-1-general-wildcard-application"></a>Scenario 1: Allmänna jokertecken-program

I det här scenariot har du tre olika program som du vill publicera:

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Alla tre program:

- Används av alla användare
- Använd *integrerad Windows-autentisering*
- Har samma egenskaper

Du kan publicera programmet jokertecken med hjälp av stegen som beskrivs i [Publicera program med hjälp av Azure AD-programproxy](application-proxy-add-on-premises-application.md). Det här scenariot förutsätter:

- En klient med följande ID: `000aa000-11b1-2ccc-d333-4444eee4444e`
- En verifierad domän med namnet `adventure-works.com` har kon figurer ATS.
- En **CNAME** -post som pekar `*.adventure-works.com` till `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` har skapats.

Följ de [dokumenterade stegen](application-proxy-add-on-premises-application.md)och skapa ett nytt program för programproxy i din klient organisation. I det här exemplet är jokertecknet i följande fält:

- Intern URL:

    ![Exempel: jokertecken i intern URL](./media/application-proxy-wildcard/42.png)

- Externa URL: en:

    ![Exempel: jokertecken i extern URL](./media/application-proxy-wildcard/43.png)

- Intern program-SPN:

    ![Exempel: jokertecken i SPN-konfigurationen](./media/application-proxy-wildcard/44.png)

Genom att publicera programmet jokertecken kan du nu komma åt dina tre program genom att gå till de URL: er som du använder till (till exempel `travel.adventure-works.com`).

Konfigurationen implementerar följande struktur:

![Visar den struktur som implementeras i exempel konfigurationen](./media/application-proxy-wildcard/05.png)

| Färg | Beskrivning |
| ---   | ---         |
| Blå  | Program som uttryckligen publiceras och visas i Azure Portal. |
| Grå  | Program du kan nås via det överordnade programmet. |

## <a name="scenario-2-general-wildcard-application-with-exception"></a>Scenario 2: Allmänna jokertecken-program med undantag

I det här scenariot har du förutom de tre allmänna programmen i ett annat program, `finance.adventure-works.com`, som endast bör vara tillgängliga via finansiell division. Med den aktuella programmet strukturen är ekonomi-program tillgänglig via jokertecken-program och av alla anställda. Om du vill ändra detta undanta ditt program från ditt jokertecken genom att konfigurera Ekonomi som ett separat program med mer restriktiva behörigheter.

Du måste se till att det finns en CNAME-post som pekar `finance.adventure-works.com` till den programspecifika slut punkten som anges på sidan programproxy för programmet. I det här scenariot `finance.adventure-works.com` pekar på `https://finance-awcycles.msappproxy.net/`.

Efter de [dokumenterade stegen](application-proxy-add-on-premises-application.md)kräver det här scenariot följande inställningar:

- I den **interna webb adressen**ställer du in **ekonomi** i stället för jokertecken.

    ![Exempel: Ange ekonomi i stället för ett jokertecken i en intern URL](./media/application-proxy-wildcard/52.png)

- I den **externa webb adressen**ställer du in **ekonomi** i stället för jokertecken.

    ![Exempel: Ange ekonomi i stället för ett jokertecken i en extern URL](./media/application-proxy-wildcard/53.png)

- Internt program-SPN du anger **ekonomi** i stället för ett jokertecken.

    ![Exempel: Ange ekonomi i stället för ett jokertecken i SPN-konfigurationen](./media/application-proxy-wildcard/54.png)

Den här konfigurationen implementerar följande scenario:

![Visar konfigurationen som implementeras i exempel scenariot](./media/application-proxy-wildcard/09.png)

Eftersom `finance.adventure-works.com` är en mer viss URL än `*.adventure-works.com`har den företräde. Användare som navigerar till `finance.adventure-works.com` har den erfarenhet som anges i programmet ekonomi resurser. I det här fallet kan endast ekonomi personal komma åt `finance.adventure-works.com`.

Om du har flera program publicerade för finansiering och du har `finance.adventure-works.com` som en verifierad domän, kan du publicera ett annat program med jokertecken `*.finance.adventure-works.com`. Eftersom detta är mer specifik än det allmänna `*.adventure-works.com`har det företräde om en användare har åtkomst till ett program i ekonomi domänen.

## <a name="next-steps"></a>Nästa steg

- Mer information om **anpassade domäner**finns [i arbeta med anpassade domäner i Azure AD-programproxy](application-proxy-configure-custom-domain.md).
- Mer information om hur du **publicerar program**finns i [Publicera program med hjälp av Azure AD-programproxy](application-proxy-add-on-premises-application.md)
