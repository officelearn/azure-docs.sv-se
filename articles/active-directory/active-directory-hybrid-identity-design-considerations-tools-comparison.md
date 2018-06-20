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
ms.date: 05/31/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 10774e7ca1168a58e8c8d47e6a7295ff727fa1cd
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801457"
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Hybrididentitet: Jämförelse av katalogintegreringsverktyg
Katalogintegreringsverktygen har vuxit och utvecklats med åren.  Avsikten med det här dokumentet är att ge dig en samlad vy över dessa verktyg och en jämförelse av de funktioner som är tillgängliga i vart och ett.

<!-- The hardcoded link is a workaround for campaign ids not working in acom links-->

> [!NOTE]
> Azure AD Connect innehåller komponenter och funktioner som tidigare gavs ut som DirSync och AAD Sync. Dessa verktyg ges inte längre ut individuellt. Alla framtida förbättringar tas med i Azure AD Connect-uppdateringarna så att du alltid vet var de senaste funktionerna finns.
> 
> DirSync och Azure AD Sync är föråldrade. Mer information finns [här](active-directory-aadconnect-dirsync-deprecated.md).
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
| Ansluta till en enda lokal LDAP-katalog | | | |● |● |
| Ansluta till flera lokala LDAP-kataloger |  | | |● |● |
| Ansluta till lokalt AD och lokala LDAP-kataloger | | | |● |● |
| Ansluta till anpassade system (SQL, Oracle, MySQL osv.) |FR | | |● |● |
| Synkronisera kunddefinierade attribut (katalogtillägg) |● | | | | |
| Ansluta till lokalt HR (SAP, Oracle eBusiness, PeopleSoft osv.) |FR | | |● |● |
| Stöder FIM-synkroniseringsregler och kopplingar för etablering i lokala system. | | | |● |● |


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
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).

