---
title: Vanliga frågor och svar – Azure Active Directory Domain Services | Microsoft Docs
description: Vanliga frågor och svar om Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: ergreenl
ms.openlocfilehash: a91c015561ca173412e4d6710d5531a7d084bfd0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60359100"
---
# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Azure Active Directory Domain Services: Vanliga frågor och svar
Den här sidan får du svar på vanliga frågor om Azure Active Directory Domain Services. Kontrollera tillbaka för uppdateringar.

## <a name="troubleshooting-guide"></a>Felsökningsguide
Referera till den [felsökningsguide för](active-directory-ds-troubleshooting.md) efter lösningar på vanliga problem med att konfigurera och administrera Azure AD Domain Services.

## <a name="configuration"></a>Konfiguration
### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Kan jag skapa flera hanterade domäner för en enda Azure AD-katalog?
Nej. Du kan bara skapa en enda hanterad domän som underhålls av Azure AD Domain Services för en enda Azure AD-katalog.  

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Kan jag aktivera Azure AD Domain Services i ett Azure Resource Manager-nätverk?
Ja. Azure AD Domain Services kan aktiveras i ett Azure Resource Manager-nätverk. Klassiska Azure-nätverk stöds inte längre för att skapa nya hanterade domäner.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Kan jag migrera min befintliga hanterade domän från ett klassiskt virtuellt nätverk till ett virtuellt nätverk för Resource Manager?
För närvarande inte. Microsoft levererar en mekanism för att migrera dina befintliga hanterade domän från ett klassiskt virtuellt nätverk till ett virtuellt nätverk för Resource Manager i framtiden.

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Kan jag aktivera Azure AD Domain Services i en Azure-CSP (Cloud Solution Provider)-prenumeration?
Ja. Se hur du kan aktivera [Azure AD Domain Services i Azure CSP-prenumerationer](active-directory-ds-csp.md).

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Kan jag aktivera Azure AD Domain Services i en federerad Azure AD-katalog? Jag synkroniserar inte lösenord autentiseringshasher till Azure AD. Kan jag aktivera Azure AD Domain Services för den här katalogen?
Nej. Azure AD Domain Services behöver åtkomst till lösenords-hash av användarkonton, autentisera användare via NTLM eller Kerberos. I en federerad katalog lagras hashvärden för lösenord inte i Azure AD-katalog. Därför fungerar inte Azure AD Domain Services med sådana Azure AD-kataloger.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Kan jag göra Azure AD Domain Services i flera virtuella nätverk i min prenumeration?
Själva tjänsten stöder inte det här scenariot. Din hanterade domän finns i endast ett virtuellt nätverk i taget. Du kan dock konfigurera anslutningar mellan flera virtuella nätverk för att exponera Azure AD Domain Services till andra virtuella nätverk. Se hur du [ansluta virtuella nätverk i Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Kan jag aktivera Azure AD Domain Services med PowerShell?
Ja. Se [hur att aktivera Azure AD Domain Services med hjälp av PowerShell](active-directory-ds-enable-using-powershell.md).

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Kan jag aktivera Azure AD Domain Services med hjälp av en Resource Manager-mallen?
Nej, det går inte för närvarande att aktivera Azure AD Domain Services med hjälp av en mall. I stället använda PowerShell, se [hur att aktivera Azure AD Domain Services med hjälp av PowerShell](active-directory-ds-enable-using-powershell.md).

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Kan jag lägga till domänkontrollanter i en Azure AD Domain Services-hanterad domän?
Nej. Den domän som tillhandahålls av Azure AD Domain Services är en hanterad domän. Du inte behöver etablera, konfigurera eller på annat sätt hantera domänkontrollanter för den här domänen – tillhandahålls dessa hanteringsaktiviteter som en tjänst av Microsoft. Därför kan du lägga till ytterligare domänkontrollanter (skrivskyddad eller skrivskyddade) för den hanterade domänen.

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>Gästanvändare bjudits in till min katalog kan använda Azure AD Domain Services?
Nej. Gästanvändare bjudits in till din Azure AD directory med hjälp av den [Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) inbjudan processen synkroniseras till din hanterade domän i Azure AD Domain Services. Lösenord för dessa användare lagras inte i Azure AD-katalogen. Därför kan Azure AD Domain Services har inget sätt att synkronisera NTLM och Kerberos-hashvärden för dessa användare till din hanterade domän. Därför kan kan inte dessa användare logga in på den hanterade domänen eller ansluta datorer till den hanterade domänen.

## <a name="administration-and-operations"></a>Administration och åtgärder
### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Kan jag ansluta till domänkontrollanten för min hanterade domänen med hjälp av fjärrskrivbord?
Nej. Du har inte behörighet att ansluta till domänkontrollanter för den hanterade domänen via fjärrskrivbord. Medlemmar i gruppen ”AAD DC-administratörer” kan administrera den hanterade domänen med AD verktyg för fjärrserveradministration, till exempel Active Directory Administration Center (ADAC) eller AD PowerShell. De här verktygen är installerade med hjälp av funktionen ”Verktyg för fjärrserveradministration” på en Windows-server som är anslutna till den hanterade domänen.

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Jag har aktiverat Azure AD Domain Services. Vilka användarkonto kan jag använda för domänen ansluta datorer till den här domänen?
Medlemmar i den administrativa gruppen ”AAD DC-administratörer” kan domänanslutning datorer. Dessutom har medlemmar i den här gruppen beviljats fjärrskrivbordsåtkomst till datorer som har anslutits till domänen.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Måste jag administratörsbehörighet för domänen för den hanterade domänen tillhandahålls av Azure AD Domain Services?
Nej. Du har inte beviljats administratörsbehörighet på den hanterade domänen. Både domän Administrator och Enterprise Administrator privilegier är inte tillgängliga för dig att använda i domänen. Medlemmar i domänadministratör eller enterprise administratörsgrupper i din lokala Active Directory har också inte beviljats administratörsbehörighet för domänen/enterprise på den hanterade domänen.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Kan jag ändra medlemskap i grupper via LDAP eller andra AD Administrationsverktyg på hanterade domäner?
Nej. Gruppmedlemskap kan inte ändras på domäner som underhålls av Azure AD Domain Services. Detsamma gäller för användarattribut. Du kan dock ändra gruppmedlemskap eller användarattribut i Azure AD eller på din lokala domän. Dessa ändringar synkroniseras automatiskt till Azure AD Domain Services.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Hur lång tid tar det innan ändringar jag göra i min Azure AD-katalog som ska visas i min hanterade domänen?
Ändringar som gjorts i Azure AD-katalogen med Azure AD-Användargränssnittet eller PowerShell synkroniseras till din hanterade domän. Den här synkroniseringsprocessen körs i bakgrunden. När den inledande synkroniseringen är klar tar normalt ungefär 20 minuter för ändringar som görs i Azure AD återspeglas i din hanterade domän.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Kan jag utöka schemat för den hanterade domänen tillhandahålls av Azure AD Domain Services?
Nej. Schemat administreras av Microsoft för den hanterade domänen. Schematillägg stöds inte av Azure AD Domain Services.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Kan jag ändra eller lägga till DNS-poster i min hanterade domänen?
Ja. Medlemmar i gruppen ”AAD DC-administratörer” beviljas behörighet för DNS-administratören om du vill ändra DNS-poster i den hanterade domänen. De kan använda DNS-hanterarens konsol på en dator som kör Windows Server som är anslutna till den hanterade domänen för att hantera DNS. Installera ”DNS-serververktyg”, vilket är en del av funktionen ”Verktyg för fjärrserveradministration' valfritt på servern för att använda DNS-hanteringskonsolen. Mer information om [verktyg för administration, övervakning och felsökning DNS](https://technet.microsoft.com/library/cc753579.aspx) finns på TechNet.

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Vad är livslängd lösenordsprincip på en hanterad domän?
Standardlivstid för lösenord på en Azure AD Domain Services-hanterad domän är 90 dagar. Det här lösenordet livslängd är inte synkroniserad med livslängden för lösenord som konfigurerats i Azure AD. Därför kan du ha en situation där användarnas lösenord upphör att gälla i den hanterade domänen, men fortfarande är giltiga i Azure AD. I sådana fall kan användare måste ändra sina lösenord i Azure AD och det nya lösenordet synkroniseras till din hanterade domän. Dessutom synkroniseras 'lösenord-gör-not-upphör att gälla' och 'user-must-change-password-at-next-logon' attribut för användarkonton som inte till din hanterade domän.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Tillhandahåller Azure AD Domain Services AD kontoutelåsningsskydd?
Ja. Fem ogiltig lösenordsförsök inom två minuter i den hanterade domänen orsaka ett användarkonto blir utelåst under 30 minuter. Efter 30 minuter låses användarkontot automatiskt. Ogiltig lösenordsförsök i den hanterade domänen låser inte ut användarkontot i Azure AD. Användarkontot är låst endast i Azure AD Domain Services-hanterade domänen.

## <a name="billing-and-availability"></a>Fakturering och tillgänglighet
### <a name="is-azure-ad-domain-services-a-paid-service"></a>Är Azure AD Domain Services en betalda tjänsten?
Ja. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/active-directory-ds/).

### <a name="is-there-a-free-trial-for-the-service"></a>Finns det en kostnadsfri utvärderingsversion för tjänsten?
Den här tjänsten ingår i den kostnadsfria utvärderingsversionen för Azure. Du kan registrera dig för en [kostnadsfri dagars utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Kan jag pausar en Azure AD Domain Services-hanterad domän? 
Nej. När du har aktiverat en Azure AD Domain Services-hanterad domän, är tjänsten tillgänglig i det valda virtuella nätverket förrän du inaktivera/ta bort den hanterade domänen. Det går inte att pausa tjänsten. Fakturering fortsätter timme tills du tar bort den hanterade domänen.

### <a name="can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>Kan jag redundans Azure AD Domain Services till en annan region för en DR-händelse?
Nej.  Azure AD Domain Services ger för närvarande inte en geo-redundant distributionsmodell. Den är begränsad till ett enda virtuellt nätverk i en Azure-region. Om du vill använda flera Azure-regioner, måste du köra dina Active Directory-domänkontrollanter på virtuella Azure IaaS-datorer.  Arkitekturvägledning finns [här](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain).

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Får jag Azure AD Domain Services som en del av Enterprise Mobility Suite (EMS)? Behöver jag Azure AD Premium för att använda Azure AD Domain Services?
Nej. Azure AD Domain Services är en betala per användning Azure-tjänst och ingår inte i EMS. Azure AD Domain Services kan användas med alla utgåvor av Azure AD (kostnadsfri, Basic och, Premium). Du debiteras per timme, beroende på användning.

### <a name="what-azure-regions-is-the-service-available-in"></a>Vilka Azure-regioner är tillgängliga i tjänsten?
Referera till den [Azure-tjänster efter region](https://azure.microsoft.com/regions/#services/) och se en lista över vilka Azure-regioner där Azure AD Domain Services är tillgänglig.
