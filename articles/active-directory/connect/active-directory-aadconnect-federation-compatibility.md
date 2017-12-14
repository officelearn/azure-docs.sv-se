---
title: "Kompatibilitetslista för Azure AD-federation"
description: "Den här sidan har icke-Microsoft leverantörer som kan användas för att implementera enkel inloggning."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: curtand
ms.assetid: 22c8693e-8915-446d-b383-27e9587988ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: billmath
ms.openlocfilehash: 86c247b6a9d197e89040109ac6b8686e3310dbf5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-federation-compatibility-list"></a>Kompatibilitetslista för Azure AD-federation
Azure Active Directory tillhandahåller enkel inloggning på och förbättrad säkerhet för åtkomst för Office 365 och andra Microsoft Online-tjänster för hybrid och endast molnbaserad implementeringar utan några icke-Microsoft-lösning. Office 365, som de flesta av Microsofts onlinetjänster, är integrerad med Azure Active Directory för katalogtjänster, autentisering och auktorisering. Azure Active Directory ger även enkel inloggning till tusentals SaaS-program och lokala webbprogram. Se Azure Active Directory-programgalleriet för SaaS-program som stöds.

Det här avsnittet innehåller riktlinjer för hur du konfigurerar enkel inloggning för sina Windows Server Active Directory-användare med Microsoft Online services med hjälp av icke-Microsoft leverantörer från ”Azure Active Directory federation kompatibilitetslistan” nedan för organisationer som har investerat i federationslösningar för icke-Microsoft. 

![](./media/active-directory-aadconnect-federation-compatibility/oxford2.jpg)   
[Datorgrupp för Oxford](http://oxfordcomputergroup.com/), en tredje part för Microsoft, testas dessa enkel inloggning med hjälp av icke-Microsoft leverantörer mot en uppsättning vanliga användningsområden med Azure Active Directory.

Information om hur du kan få tredjeparts identitetsprovider som anges här kontakta Oxford datorgrupp på [ idp@oxfordcomputergroup.com ](mailto:idp@oxfordcomputergroup.com).

> [!IMPORTANT]
> Datorgrupp för Oxford testats federation funktioner av dessa scenarier för enkel inloggning. Datorgrupp för Oxford kunde inte utföra några testning av synkroniseringen, tvåfaktorsautentisering, osv. komponenter av dessa scenarier för enkel inloggning.
> 
> Användning av inloggning med Alternativt ID i UPN-har också inte testats i det här programmet.
> 
> 

* [Azure Active Directory](#azure-active-directory)
* [AuthAnvil enkel inloggning 4.5](#authanvil-single-sign-on-45)
* [BIG-IP-Adressen med åtkomst principhanteraren BIG-IP ver. 11,3 x – x 11,6](#big-ip-with-access-policy-manager-big-ip-ver-113x--116x) 
* [BitGlass](#bitglass)
* [Säker CA-moln](#ca-secure-cloud) 
* [Kanada SiteMinder 12,52](#ca-siteminder-1252-sp1-cumulative-release-4) 
* [Centrify](#centrify) 
* [Dell en identitet molnet hanteraren v7.1](#dell-one-identity-cloud-access-manager-v71) 
* [DigitalPersona sammansatt autentisering](#digitalpersona-composite-authentication)
* [ForgeRock identitet plattform Access Management V5.x](#forgerock-identity-platform-access-management-v5x)
* [IBM Tivoli federerad identitet Manager 6.2.2](#ibm-tivoli-federated-identity-manager-622) 
* [IceWall Federation Version 3.0](#icewall-federation-version-30) 
* [Memority](#memority)
* [NetIQ hanteraren 4.x](#netiq-access-manager-4x) 
* [Okta](#okta) 
* [OneLogin](#onelogin) 
* [Optimal IDM virtuella Identity Server Federation Services](#optimal-idm-virtual-identity-server-federation-services) 
* [PingFederate 6.11, 7.2, 8.x](#pingfederate-611-72-8x)
* [RadiantOne CFS 3.0](#radiantone-cfs-30) 
* [Sailpoint IdentityNow](#sailpoint-identitynow)
* [SecureAuth IdP 7.2.0](#secureauth-idp-720) 
* [Registrera & Gå 5.3](#signgo-53) 
* [SoftBank teknik onlinetjänst Gate](#softbank)
* [VMware arbetsytan en](#vmware-workspace-one)



> [!IMPORTANT]
> Eftersom dessa produkter från tredje part, tillhandahåller Microsoft inte stöd för distribution, konfiguration, felsökning, bästa praxis, etc. problem och frågor om dessa identitetsleverantörer. Support och frågor om dessa identitetsleverantörer kontakta stöds tredje-parter.
> 
> Dessa tredje parts identitetsleverantörer har testats för samverkan med Microsofts molntjänster med WS-Federation och WS-Trust-protokoll. Testa innehöll inte med SAML-protokoll.
> 


## <a name="azure-active-directory"></a>Azure Active Directory

Följande är ett scenario stöd matrix för den här inloggning: 

| Client | Support | Undantag |
| --- | --- | --- |
| Webbaserade klienter, till exempel Exchange Web Access och SharePoint Online |Stöds |Ingen |
| Rich-klientprogram, till exempel Lync, Office-prenumeration, CRM |Stöds |Ingen |
| E-post-RTF-klienter, till exempel Outlook och ActiveSync |Stöds |Ingen |
| Moderna program som använder ADAL, till exempel Office 2016 |Stöds |Ingen |

Mer information om hur du använder Azure Active Directory med AD FS finns [Active Directory Federation Services (ADFS)](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs).

Läs mer om hur du använder Azure Active Directory med Lösenordssynkronisering [Azure AD Connect](active-directory-aadconnect.md).

## <a name="authanvil-single-sign-on-45"></a>AuthAnvil enkel inloggning 4.5

Följande är ett scenario stöd matrix för den här enkel inloggning:

| Client | Support | Undantag |
| --- | --- | --- |
| Webbaserade klienter, till exempel Exchange Web Access och SharePoint Online |Stöds |Integrerad Windows-autentisering stöds inte |
| Rich-klientprogram, till exempel Lync, Office-prenumeration, CRM |Stöds |Integrerad Windows-autentisering stöds inte |
| E-post-RTF-klienter, till exempel Outlook och ActiveSync |Stöds |Ingen |

Mer information finns i [AuthAnvil enkel inloggning.](https://help.scorpionsoft.com/entries/26538603-How-can-I-Configure-Single-Sign-On-for-Office-365-).


## <a name="big-ip-with-access-policy-manager-big-ip-ver-113x--116x"></a>BIG-IP-Adressen med åtkomst principhanteraren BIG-IP ver. 11,3 x – x 11,6

Följande är ett scenario stöd matrix för den här enkel inloggning: 

| Client | Support | Undantag |
| --- | --- | --- |
| Webbaserade klienter, till exempel Exchange Web Access och SharePoint Online |Stöds |Ingen |
| Rich-klientprogram, till exempel Lync, Office-prenumeration, CRM |Stöds inte |Stöds inte |
| E-post-RTF-klienter, till exempel Outlook och ActiveSync |Stöds |Ingen |

Läs mer om principhanterare för BIG-IP-åtkomst [principhanterare för BIG-IP-åtkomst.](https://f5.com/products/modules/access-policy-manager) 

Hanteraren för BIG-IP-åtkomst-instruktioner för hur du konfigurerar detta STS att tillhandahålla enkel inloggning till din Active Directory-användare hämta PDF-filen [BIG-IP](http://www.f5.com/pdf/deployment-guides/microsoft-office-365-idp-dg.pdf).

## <a name="bitglass"></a>BitGlass

Följande är ett scenario stöd matrix för den här enkel inloggning:

| Client | Support | Undantag |
| --- | --- | --- |
| Webbaserade klienter, till exempel Exchange Web Access och SharePoint Online |Stöds |Integrerad Windows-autentisering stöds inte |
| Rich-klientprogram, till exempel Lync, Office-prenumeration, CRM |Stöds |Integrerad Windows-autentisering stöds inte |
| E-post-RTF-klienter, till exempel Outlook och ActiveSync |Stöds |Ingen |

Läs mer om BitGlass [BitGlass](http://www.bitglass.com).

## <a name="ca-secure-cloud"></a>Säker CA-moln

Följande är ett scenario stöd matrix för den här enkel inloggning:

| Client | Support | Undantag |
| --- | --- | --- |
| Webbaserade klienter, till exempel Exchange Web Access och SharePoint Online |Stöds |Integrerad Windows-autentisering stöds inte |
| Rich-klientprogram, till exempel Lync, Office-prenumeration, CRM |Stöds |Integrerad Windows-autentisering stöds inte |
| E-post-RTF-klienter, till exempel Outlook och ActiveSync |Stöds |Ingen |

Mer information om Certifikatutfärdare skydda molnet finns [CA skydda molnet](http://www.ca.com/us/products/security-as-a-service.aspx).

## <a name="ca-siteminder-1252-sp1-cumulative-release-4"></a>Kanada SiteMinder 12,52 SP1 kumulativa Release 4

Följande är ett scenario stöd matrix för den här enkel inloggning: 

| Client | Support | Undantag |
| --- | --- | --- |
| Webbaserade klienter, till exempel Exchange Web Access och SharePoint Online |Stöds |Ingen |
| Rich-klientprogram, till exempel Lync, Office-prenumeration, CRM |Stöds |Ingen |
| E-post-RTF-klienter, till exempel Outlook och ActiveSync |Stöds |Ingen |

Läs mer om Certifikatutfärdaren SiteMinder [CA SiteMinder Federation](http://www.ca.com/us/products/ca-single-sign-on.html). 

## <a name="centrify"></a>Centrify

Följande är ett scenario stöd matrix för den här enkel inloggning:

| Client | Support | Undantag |
| --- | --- | --- |
| Webbaserade klienter, till exempel Exchange Web Access och SharePoint Online |Stöds |Ingen |
| Rich-klientprogram, till exempel Lync, Office-prenumeration, CRM |Stöds |Ingen |
| E-post-RTF-klienter, till exempel Outlook och ActiveSync |Stöds |Klienten åtkomstkontroll stöds inte |

Läs mer om Centrify [Centrify](http://www.centrify.com/cloud/apps/single-sign-on-for-office-365.asp).

## <a name="dell-one-identity-cloud-access-manager-v71"></a>Dell en identitet molnet hanteraren v7.1

Följande är ett scenario stöd matrix för den här enkel inloggning:

| Client | Support | Undantag |
| --- | --- | --- |
| Webbaserade klienter, till exempel Exchange Web Access och SharePoint Online |Stöds |Ingen |
| Rich-klientprogram, till exempel Lync, Office-prenumeration, CRM |Stöds |Ingen |
| E-post-RTF-klienter, till exempel Outlook och ActiveSync |Stöds |Ingen |

Läs mer om Dell en identitet molnet Accessmanager [Dell en identitet åtkomst molnhanteraren](http://software.dell.com/products/cloud-access-manager).

 Anvisningar om hur du konfigurerar den här STS att tillhandahålla enkel inloggning för din Office 365-användare, se [konfigurera Office 365-användare](http://documents.software.dell.com/dell-one-identity-cloud-access-manager/7.1/how-to-configure-microsoft-office-365). 

## <a name="digitalpersona-composite-authentication"></a>DigitalPersona sammansatt autentisering  

Följande är ett scenario stöd matrix för den här enkel inloggning:

| Client | Support | Undantag |
| --- | --- | --- |
| Webbaserade klienter, till exempel Exchange Web Access och SharePoint Online |Stöds |Integrerad Windows-autentisering stöds inte|
| Rich-klientprogram, till exempel Lync, Office-prenumeration, CRM |Stöds |Integrerad Windows-autentisering stöds inte|
| E-post-RTF-klienter, till exempel Outlook och ActiveSync |Stöds |Ingen |

Mer information finns i [DigitalPersona sammansatt autentisering](http://www.crossmatch.com/uploadedFiles/Support/Reference_Material/DigitalPersona-Office-365-Deployment-Guide.pdf).

## <a name="forgerock-identity-platform-access-management-v5x"></a>ForgeRock identitet plattform Access Management V5.x

Följande är ett scenario stöd matrix för den här enkel inloggning:

| Client | Support | Undantag |
| --- | --- | --- |
| Webbaserade klienter, till exempel Exchange Web Access och SharePoint Online |Stöds |Ingen|
| Rich-klientprogram, till exempel Lync, Office-prenumeration, CRM |Stöds |Ingen|
| E-post-RTF-klienter, till exempel Outlook och ActiveSync |Stöds |Ingen |

Mer information finns i [ForgeRock identitet plattform Access Management V5.x](https://backstage.forgerock.com/knowledge/kb/article/a98278517).

## <a name="ibm-tivoli-federated-identity-manager-622"></a>IBM Tivoli federerad identitet Manager 6.2.2

Följande är ett scenario stöd matrix för den här enkel inloggning: 

| Client | Support | Undantag |
| --- | --- | --- |
| Webbaserade klienter, till exempel Exchange Web Access och SharePoint Online |Stöds |Ingen |
| Rich-klientprogram, till exempel Lync, Office-prenumeration, CRM |Stöds |Ingen |
| E-post-RTF-klienter, till exempel Outlook och ActiveSync |Stöds |Ingen |

Läs mer om IBM Tivoli federerad identitet Manager [IBM Security åtkomst Manager for Microsoft Applications](http://www-01.ibm.com/support/docview.wss?uid=swg24029517).

## <a name="icewall-federation-version-30"></a>IceWall Federation Version 3.0

Följande är ett scenario stöd matrix för den här enkel inloggning:

| Client | Support | Undantag |
| --- | --- | --- |
| Webbaserade klienter, till exempel Exchange Web Access och SharePoint Online |Stöds |Integrerad Windows-autentisering stöds inte |
| Rich-klientprogram, till exempel Lync, Office-prenumeration, CRM |Stöds |Integrerad Windows-autentisering stöds inte |
| E-post-RTF-klienter, till exempel Outlook och ActiveSync |Stöds |Ingen |

Läs mer om IceWall Federation [IceWall Federation Version 3.0](http://h50146.www5.hp.com/products/software/security/icewall/eng/federation/) och [IceWall Federation med Office 365](http://h50146.www5.hp.com/products/software/security/icewall/federation/office365.html).

## <a name="memority"></a>Memority

Följande är ett scenario stöd matrix för den här inloggning: 

| Client | Support | Undantag |
| --- | --- | --- |
| Webbaserade klienter, till exempel Exchange Web Access och SharePoint Online |Stöds |Ingen |
| Rich-klientprogram, till exempel Lync, Office-prenumeration, CRM |Stöds |Ingen |
| E-post-RTF-klienter, till exempel Outlook och ActiveSync |Stöds |Ingen |

Läs mer om hur du använder Memority [Memority](http://www.memority.com).


## <a name="netiq-access-manager-4x"></a>NetIQ hanteraren 4.x

Följande är ett scenario stöd matrix för den här enkel inloggning:

| Client | Support | Undantag |
| --- | --- | --- |
| Webbaserade klienter, till exempel Exchange Web Access och SharePoint Online |Stöds |Ingen|
| Rich-klientprogram, till exempel Lync, Office-prenumeration, CRM |Stöds |Ingen|
| E-post-RTF-klienter, till exempel Outlook och ActiveSync |Stöds |Ingen |

Mer information finns i [NetIQ hanteraren](https://www.netiq.com/documentation/access-manager-43/admin/data/b65ogn0.html#b12iqp0m).

## <a name="okta"></a>Okta

Följande är ett scenario stöd matrix för den här enkel inloggning: 

| Client | Support | Undantag |
| --- | --- | --- |
| Webbaserade klienter, till exempel Exchange Web Access och SharePoint Online |Stöds |Integrerad Windows-autentisering kräver installation av ytterligare en webbserver och Okta-tjänstprogrammet. |
| Rich-klientprogram, till exempel Lync, Office-prenumeration, CRM |Stöds |Integrerad Windows-autentisering |
| E-post-RTF-klienter, till exempel Outlook och ActiveSync |Stöds |Ingen |

Läs mer om Okta [Okta](https://www.okta.com/).

## <a name="onelogin"></a>OneLogin

Följande är ett scenario stöd matrix för den här enkel inloggning: 

| Client | Support | Undantag |
| --- | --- | --- |
| Webbaserade klienter, till exempel Exchange Web Access och SharePoint Online |Stöds |Integrerad Windows-autentisering |
| Rich-klientprogram, till exempel Lync, Office-prenumeration, CRM |Stöds |Integrerad Windows-autentisering |
| E-post-RTF-klienter, till exempel Outlook och ActiveSync |Stöds |Ingen |

Läs mer om OneLogin [OneLogin](https://www.onelogin.com/).

## <a name="optimal-idm-virtual-identity-server-federation-services"></a>Optimal IDM virtuella Identity Server Federation Services

Följande är scenariot supportmatris denna enkel inloggning:

| Client | Support | Undantag |
| --- | --- | --- |
| Webbaserade klienter, till exempel Exchange Web Access och SharePoint Online |Stöds |Ingen |
| Rich-klientprogram, till exempel Lync, Office-prenumeration, CRM |Stöds |Integrerad Windows-autentisering |
| E-post-RTF-klienter, till exempel Outlook och ActiveSync |Stöds |

För mer information om klientåtkomst principer Se [att begränsa åtkomst till Office 365-tjänster baserat på plats av klienten](https://technet.microsoft.com/library/hh526961.aspx).





## <a name="pingfederate-611-72-8x"></a>PingFederate 6.11, 7.2, 8.x

Följande är ett scenario stöd matrix för den här enkel inloggning:

| Client | Support | Undantag |
| --- | --- | --- |
| Webbaserade klienter, till exempel Exchange Web Access och SharePoint Online |Stöds |Ingen |
| Rich-klientprogram, till exempel Lync, Office-prenumeration, CRM |Stöds |Ingen |
| E-post-RTF-klienter, till exempel Outlook och ActiveSync |Stöds |Ingen |

PingFederate-instruktioner om hur du konfigurerar den här STS att tillhandahålla enkel inloggning till Active Directory-användare finns i något av följande: 

- [PingFederate 6.11](http://go.microsoft.com/fwlink/?LinkID=266321)
- [PingFederate 7.2](http://documentation.pingidentity.com/display/PF72/PingFederate+7.2)
- [PingFederate 8.x](http://documentation.pingidentity.com/display/PFS/SSO+to+Office+365+Introduction)

## <a name="radiantone-cfs-30"></a>RadiantOne CFS 3.0

Följande är ett scenario stöd matrix för den här enkel inloggning: 

| Client | Support | Undantag |
| --- | --- | --- |
| Webbaserade klienter, till exempel Exchange Web Access och SharePoint Online |Stöds |Ingen |
| Rich-klientprogram, till exempel Lync, Office-prenumeration, CRM |Stöds |Integrerad Windows-autentisering |
| E-post-RTF-klienter, till exempel Outlook och ActiveSync |Stöds |Ingen |

Läs mer om RadiantOne CFS [RadiantOne CFS](http://www.radiantlogic.com/products/radiantone-cfs/).

## <a name="sailpoint-identitynow"></a>Sailpoint IdentityNow

Följande är ett scenario stöd matrix för den här enkel inloggning:

| Client | Support | Undantag |
| --- | --- | --- |
| Webbaserade klienter, till exempel Exchange Web Access och SharePoint Online |Stöds |Integrerad Windows-autentisering stöds inte |
| Rich-klientprogram, till exempel Lync, Office-prenumeration, CRM |Stöds |Integrerad Windows-autentisering stöds inte |
| E-post-RTF-klienter, till exempel Outlook och ActiveSync |Stöds |Ingen |

Mer information finns i [Sailpoint IdentityNow](https://www.sailpoint.com/idaas-identity-as-a-service-identitynow/).

## <a name="secureauth-idp-720"></a>SecureAuth IdP 7.2.0

Följande är ett scenario stöd matrix för den här enkel inloggning: 

| Client | Support | Undantag |
| --- | --- | --- |
| Webbaserade klienter, till exempel Exchange Web Access och SharePoint Online |Stöds |Ingen |
| Rich-klientprogram, till exempel Lync, Office-prenumeration, CRM |Stöds |Ingen |
| E-post-RTF-klienter, till exempel Outlook och ActiveSync |Stöds |Ingen |

Läs mer om SecureAuth [SecureAuth IdP](http://go.microsoft.com/?linkid=9845293).














## <a name="signgo-53"></a>Registrera & Gå 5.3

Följande är ett scenario stöd matrix för den här enkel inloggning:

| Client | Support | Undantag |
| --- | --- | --- |
| Webbaserade klienter, till exempel Exchange Web Access och SharePoint Online |Stöds |Kerberos-kontrakt som stöds |
| Rich-klientprogram, till exempel Lync, Office-prenumeration, CRM |Stöds |Ingen |
| E-post-RTF-klienter, till exempel Outlook och ActiveSync |Stöds |Ingen |

Signera & Gå 5.3 stöder Kerberos-autentisering via konfiguration av en Kerberos-kontrakt.  Om du behöver hjälp med den här konfigurationen kontakta Ilex eller visa i inställningsguiden [signera & Gå](http://www.ilex-international.com/docs/sign&go_wsfederation_en.pdf)

## <a name="softbank-technology-online-service-gate"></a>SoftBank teknik onlinetjänst Gate

Följande är ett scenario stöd matrix för den här enkel inloggning:

| Client | Support | Undantag |
| --- | --- | --- |
| Webbaserade klienter, till exempel Exchange Web Access och SharePoint Online |Stöds |Integrerad Windows-autentisering stöds inte |
| Rich-klientprogram, till exempel Lync, Office-prenumeration, CRM |Stöds |Integrerad Windows-autentisering stöds inte |
| E-post-RTF-klienter, till exempel Outlook och ActiveSync |Stöds |Ingen |

Läs mer om SoftBank teknik Online Service Gate [Softbank](https://www.softbanktech.jp/service/list/osg-pro-ent/)

## <a name="vmware-workspace-one"></a>VMware arbetsytan en

Följande är ett scenario stöd matrix för den här enkel inloggning:

| Client | Support | Undantag |
| --- | --- | --- |
| Webbaserade klienter, till exempel Exchange Web Access och SharePoint Online |Stöds |Integrerad Windows-autentisering stöds inte |
| Rich-klientprogram, till exempel Lync, Office-prenumeration, CRM |Stöds |Integrerad Windows-autentisering stöds inte |
| E-post-RTF-klienter, till exempel Outlook och ActiveSync |Stöds |Ingen |

Mer information om finns [VMware arbetsytan en](http://www.vmware.com/pdf/vidm-office365-saml.pdf)

