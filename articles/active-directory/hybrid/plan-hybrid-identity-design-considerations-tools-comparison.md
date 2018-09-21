---
title: 'Hybrididentitet: Jämförelse av katalogintegreringsverktyg | Microsoft Docs'
description: Den här sidan innehåller en detaljerad tabell som jämför olika katalogintegreringsverktyg som kan användas för katalogintegrering.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 1e62a4bd-4d55-4609-895e-70131dedbf52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/28/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: c7050076f80f69929b3d12f2a55b4d4a720f9896
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46301878"
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Hybrididentitet: Jämförelse av katalogintegreringsverktyg
Katalogintegreringsverktygen har vuxit och utvecklats med åren.  Avsikten med det här dokumentet är att ge dig en samlad vy över dessa verktyg och en jämförelse av de funktioner som är tillgängliga i vart och ett.

<!-- The hardcoded link is a workaround for campaign ids not working in acom links-->

> [!NOTE]
> Azure AD Connect innehåller komponenter och funktioner som tidigare gavs ut som DirSync och AAD Sync. Dessa verktyg ges inte längre ut individuellt. Alla framtida förbättringar tas med i Azure AD Connect-uppdateringarna så att du alltid vet var de senaste funktionerna finns.
> 
> DirSync och Azure AD Sync är föråldrade. Mer information finns [här](reference-connect-dirsync-deprecated.md).
> 
> 

Följande förkortningar och symboler används i tabellerna.

● = Tillgänglig nu  
FR = Framtida version  
PP = Offentlig granskning  

## <a name="on-premises-to-cloud-synchronization"></a>Synkronisering lokalt till molnet
| Funktion | Azure Active Directory Connect | Azure Active Directory Synchronization Services (AAD Sync) – STÖDS INTE LÄNGRE | Azure Active Directory Synchronization Tool (DirSync) – STÖDS INTE LÄNGRE | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Ansluta till en enda lokal AD-skog |● |● |● |● |● |
| Ansluta till flera lokala AD-skogar |● |● | |● |● |
| Ansluta till flera lokala Exchange-organisationer |● | | | | |
| Ansluta till en enda lokal LDAP-katalog |●* | | |● |● | 
| Ansluta till flera lokala LDAP-kataloger |●*  | | |● |● | 
| Ansluta till lokalt AD och lokala LDAP-kataloger |●* | | |● |● | 
| Ansluta till anpassade system (SQL, Oracle, MySQL osv.) |FR | | |● |● |
| Synkronisera kunddefinierade attribut (katalogtillägg) |● | | | | |
| Ansluta till lokalt HR (SAP, Oracle eBusiness, PeopleSoft osv.) |FR | | |● |● |
| Stöder FIM-synkroniseringsregler och kopplingar för etablering i lokala system. | | | |● |● |

 
&#42; Det finns i nuläget två alternativ för detta som stöds.  De är: 

   1. Du kan använda den allmänna LDAP-anslutningsappen och aktivera den utanför Azure AD Connect.  Det är komplex och kräver en partner för publicering och ett Premier Support-avtal för underhåll.  Det här alternativet kan hantera både enkla och flera LDAP-kataloger. 

   2. Du kan utveckla din egen lösning för att flytta objekt från LDAP till Active Directory.  Synkronisera sedan objekten med Azure AD Connect.  MIM eller FIM kan användas som en möjlig lösning för att flytta objekten. 

## <a name="cloud-to-on-premises-synchronization"></a>Synkronisering molnet till lokalt
| Funktion | Azure Active Directory Connect | Azure Active Directory Synchronization Services – STÖDS INTE LÄNGRE  | Azure Active Directory Synchronization Tool (DirSync) – STÖDS INTE LÄNGRE  | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Tillbakaskrivning av enheter |● | |● | | |
| Tillbakaskrivning av attribut (för Exchange-hybridinstallation) |● |● |● |● |● |
| Tillbakaskrivning av gruppobjekt |● | | | | |
| Tillbakaskrivning av lösenord (från lösenordsåterställning via självbetjäning (SSPR) och lösenordsändring) |● |● | | | |

## <a name="authentication-feature-support"></a>Autentiseringsfunktioner som stöds
| Funktion | Azure Active Directory Connect | Azure Active Directory Synchronization Services – STÖDS INTE LÄNGRE  | Azure Active Directory Synchronization Tool (DirSync) – STÖDS INTE LÄNGRE  | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Synkronisering av lösenords-hash för en enskild lokal AD-skog |●|●|● | | |
| Synkronisering av lösenords-hash för flera lokala AD-skogar |●|● | | | |
| Direktautentisering för enskilda lokala AD-skogar |●| | | | |
| Enkel inloggning med federation |● |● |● |● |● |
| Smidig enkel inloggning|● |||||
| Tillbakaskrivning av lösenord (från SSPR och lösenordsändring) |● |● | | | |

## <a name="set-up-and-installation"></a>Konfiguration och installation
| Funktion | Azure Active Directory Connect | Azure Active Directory Synchronization Services – STÖDS INTE LÄNGRE  | Azure Active Directory Synchronization Tool (DirSync) – STÖDS INTE LÄNGRE  | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|
| Stöder installation på en domänkontrollant |● |● |● | |
| Stöder installation med hjälp av SQL Express |● |● |● | |
| Enkel uppgradering från DirSync |● | | | |
| Lokalisering av administratörs-UX till Windows Server-språk |● |● |● | |
| Lokalisering av slutanvändar-UX till Windows Server-språk | | | |● |
| Stöd för Windows Server 2008 och Windows Server 2008 R2 |● för synkronisering, inte för federation |● |● |● |
| Stöd för Windows Server 2012 och Windows Server 2012 R2 |● |● |● |● |

## <a name="filtering-and-configuration"></a>Filtrering och konfiguration
| Funktion | Azure Active Directory Connect | Azure Active Directory Synchronization Services – STÖDS INTE LÄNGRE  | Azure Active Directory Synchronization Tool (DirSync) – STÖDS INTE LÄNGRE  | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Filtrera baserat på domäner och organisationsenheter |● |● |● |● |● |
| Filtrera baserat på objektens attributvärden |● |● |● |● |● |
| Tillåt att en minimal uppsättning attribut synkroniseras (MinSync) |● |● | | | |
| Tillåt att olika tjänstmallar används för attributflöden |● |● | | | |
| Tillåt borttagning av attribut som flödar från AD till Azure AD |● |● | | | |
| Tillåt avancerad anpassning av attributflöden |● |● | |● |● |

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).

