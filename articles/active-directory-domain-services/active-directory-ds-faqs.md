---
title: "Vanliga frågor och svar – Azure Active Directory Domain Services | Microsoft Docs"
description: "Vanliga frågor och svar om Azure Active Directory Domain Services"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: maheshu
ms.openlocfilehash: 4e95cd128c0c8ee8492e7bd224afc317101f346c
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2018
---
# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Azure Active Directory Domain Services: Vanliga frågor (FAQ)
Den här sidan svar på vanliga frågor om Azure Active Directory Domain Services. Hålla kontroll för uppdateringar.

### <a name="troubleshooting-guide"></a>Felsökningsguide
Referera till den [felsökningsguide](active-directory-ds-troubleshooting.md) efter lösningar på vanliga problem som kan uppstå när du konfigurerar eller administrera Azure AD Domain Services.

### <a name="configuration"></a>Konfiguration
#### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Kan jag skapa flera hanterade domäner för en enda Azure AD-katalog?
Nej. Du kan bara skapa en enda hanterad domän som underhålls av Azure AD Domain Services för en enda Azure AD-katalog.  

#### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Kan jag aktivera Azure AD Domain Services i ett virtuellt nätverk med Azure Resource Manager?
Ja. Azure AD Domain Services kan aktiveras i ett virtuellt nätverk med Azure Resource Manager. Klassiska virtuella Azure-nätverk stöds inte längre för att skapa nya hanterade domäner.

#### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Kan jag migrera Mina befintliga hanterade domänen från ett klassiskt virtuellt nätverk till ett virtuellt nätverk för Resource Manager?
För närvarande inte. Microsoft levererar en mekanism för att migrera din befintliga hanterade domän från ett klassiskt virtuellt nätverk till ett virtuellt nätverk för hanteraren för filserverresurser i framtiden.

#### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Kan jag aktivera Azure AD Domain Services i en CSP: N (Cloud Solution Provider) för Azure-prenumeration?
Nej. Produktteamet arbetar för att lägga till stöd för CSP-prenumerationer.

#### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-use-adfs-to-authenticate-users-for-access-to-office-365-and-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Kan jag aktivera Azure AD Domain Services i en federerad Azure AD-katalog? Jag använder AD FS för att autentisera användare för åtkomst till Office 365 och synkroniseras inte lösenordshashvärden till Azure AD. Kan jag aktivera Azure AD Domain Services för den här katalogen?
Nej. Azure AD Domain Services behöver åtkomst till lösenordshashvärden av användarkonton, kan autentisera användarna med NTLM eller Kerberos. I en federerad katalog lagras lösenordshashvärden inte i Azure AD-katalog. Därför fungerar inte Azure AD Domain Services med sådana Azure AD-kataloger.

#### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Kan jag göra Azure AD Domain Services finns i flera virtuella nätverk i min prenumeration?
Själva tjänsten stöder inte det här scenariot. Din hanterade domän finns i endast ett virtuellt nätverk i taget. Du kan dock konfigurera anslutningen mellan flera virtuella nätverk för att exponera Azure AD Domain Services till andra virtuella nätverk. Se hur du kan [ansluta virtuella nätverk i Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

#### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Kan jag aktivera Azure AD Domain Services med hjälp av PowerShell?
Ja. Se [så att aktivera Azure AD Domain Services med hjälp av PowerShell](active-directory-ds-enable-using-powershell.md).

#### <a name="is-azure-ad-domain-services-available-in-the-new-azure-portal"></a>Finns Azure AD Domain Services på den nya Azure portalen?
Ja. Azure AD Domain Services kan konfigureras med den [Azure-portalen](https://portal.azure.com). Den [klassiska Azure-portalen](https://manage.windowsazure.com) stöds inte längre.

#### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Kan jag aktivera Azure AD Domain Services med hjälp av en Resource Manager-mall?
Ja. Se [så att aktivera Azure AD Domain Services med hjälp av PowerShell](active-directory-ds-enable-using-powershell.md).

#### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Kan jag lägga till domänkontrollanter i en Azure AD Domain Services-hanterad domän?
Nej. Den domän som tillhandahålls av Azure AD Domain Services är en hanterad domän. Du inte behöver tillhandahålla, konfigurera eller på annat sätt hantera domänkontrollanter för den här domänen - tillhandahålls dessa hanteringsaktiviteter som en tjänst från Microsoft. Därför kan du lägga till ytterligare domänkontrollanter (skrivskyddad eller skrivskyddad) för den hanterade domänen.

### <a name="administration-and-operations"></a>Administration och åtgärder
#### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Kan jag ansluta till domänkontrollanten för min hanterade domänen med hjälp av fjärrskrivbord?
Nej. Du har inte behörighet att ansluta till domänkontrollanterna för den hanterade domänen via fjärrskrivbord. Medlemmar i gruppen AAD DC-administratörer kan administrera den hanterade domänen med hjälp av Administrationsverktyg för AD, till exempel Active Directory Administration Center (ADAC) eller AD PowerShell. Dessa installeras med hjälp av funktionen ”Verktyg för fjärrserveradministration' på en Windows-server som är ansluten till den hanterade domänen.

#### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Jag har aktiverat Azure AD Domain Services. Vilka användarkonto används till domänen ansluta datorer till den här domänen?
Medlemmar i den administrativa gruppen AAD DC-administratörer kan domänanslutning datorer. Dessutom kan beviljas medlemmar i gruppen fjärråtkomst till skrivbordet till datorer som har varit ansluten till domänen.

#### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Måste jag administratörsbehörighet för domänen för den hanterade domänen som tillhandahålls av Azure AD Domain Services?
Nej. Du har inte beviljats administratörsbehörighet på den hanterade domänen. Både domän-administratör och företagsadministratörsbehörighet krävs behörighet är inte tillgängliga som du kan använda i domänen. Befintliga domänadministratör eller enterprise administratörsgrupper i Azure AD-katalogen också beviljas inte administratörsbehörighet för domänen/enterprise på domänen.

#### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Kan jag ändra gruppmedlemskap via LDAP eller andra administrativa verktyg för AD på hanterade domäner?
Nej. Gruppmedlemskap kan inte ändras i domäner som underhålls av Azure AD Domain Services. Detsamma gäller för användarattribut. Du kan dock ändra gruppmedlemskap eller användarattribut i Azure AD eller på den lokala domänen. Dessa ändringar synkroniseras automatiskt till Azure AD Domain Services.

#### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Hur lång tid tar det innan ändringarna jag göra i Azure AD-katalog som ska visas i min hanterade domänen?
Ändringar i Azure AD-katalogen med Azure AD-Gränssnittet eller PowerShell synkroniseras till din hanterade domän. Den här synkroniseringsprocessen körs i bakgrunden. När en inledande synkronisering av din katalog är klar tar vanligtvis cirka 20 minuter för ändringar som gjorts i Azure AD återspeglas i din hanterade domän.

#### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Kan jag utöka schemat för den hanterade domänen som tillhandahålls av Azure AD Domain Services?
Nej. Schemat hanteras av Microsoft för den hanterade domänen. Schematillägg stöds inte av Azure AD Domain Services.

#### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Kan jag ändra eller lägga till DNS-poster i min hanterade domänen?
Ja. Medlemmar i gruppen AAD DC-administratörer beviljas behörighet för DNS-administratören om du vill ändra DNS-poster i den hanterade domänen. De kan använda DNS-hanteringskonsolen på en dator som kör Windows Server som är ansluten till den hanterade domänen för att hantera DNS. Installera 'DNS-serververktyg', vilket är en del av funktionen ”Verktyg för fjärrserveradministration' valfritt på servern för att använda DNS-hanteringskonsolen. Mer information om [verktyg för att administrera, övervaka och felsöka DNS](https://technet.microsoft.com/library/cc753579.aspx) finns på TechNet.

### <a name="billing-and-availability"></a>Fakturering och tillgänglighet
#### <a name="is-azure-ad-domain-services-a-paid-service"></a>Är Azure AD Domain Services en betald tjänst?
Ja. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/active-directory-ds/).

#### <a name="is-there-a-free-trial-for-the-service"></a>Finns det en kostnadsfri utvärderingsversion för tjänsten?
Den här tjänsten ingår i den kostnadsfria utvärderingsversionen för Azure. Du kan registrera dig för en [kostnadsfri utvärderingsversion för en månad Azure](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Kan jag pausa en Azure AD Domain Services-hanterad domän? 
Nej. När du har aktiverat en Azure AD Domain Services-hanterad domän, är tjänsten tillgänglig i det valda virtuella nätverket förrän du inaktivera/ta bort den hanterade domänen. Det går inte att pausa tjänsten. Fakturering fortsätter timme tills du tar bort den hanterade domänen.

#### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Kan jag Azure AD Domain Services som en del av Enterprise Mobility Suite (EMS)? Behöver jag Azure AD Premium för att använda Azure AD Domain Services?
Nej. Azure AD Domain Services är inte en del av EMS är en betala per användning i Azure-tjänsten. Azure AD Domain Services kan användas med alla versioner av Azure AD (ledigt, grundläggande och, Premium). Du debiteras timme, beroende på användning.

#### <a name="what-azure-regions-is-the-service-available-in"></a>Vilka Azure-regioner är tjänsten?
Referera till den [Azure-tjänster efter region](https://azure.microsoft.com/regions/#services/) sidan om du vill se en lista över Azure-regioner där Azure AD Domain Services är tillgängligt.
