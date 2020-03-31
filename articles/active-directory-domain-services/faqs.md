---
title: Vanliga frågor och svar om Azure AD Domain Services | Microsoft-dokument
description: Läsa och förstå några av de vanligaste frågorna kring konfiguration, administration och tillgänglighet för Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: iainfou
ms.openlocfilehash: a57826c79babded6e616548879a5ec0c223307d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78946437"
---
# <a name="frequently-asked-questions-faqs"></a>Vanliga frågor och svar (vanliga frågor)

Den här sidan besvarar vanliga frågor om Azure Active Directory Domain Services.

## <a name="configuration"></a>Konfiguration

* [Kan jag skapa flera hanterade domäner för en enda Azure AD-katalog?](#can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory)
* [Kan jag aktivera Azure AD Domain Services i ett virtuellt klassiskt nätverk?](#can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network)
* [Kan jag aktivera Azure AD Domain Services i ett virtuellt Azure Resource Manager-nätverk?](#can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network)
* [Kan jag migrera min befintliga hanterade domän från ett klassiskt virtuellt nätverk till ett virtuellt resurshanterarenätverk?](#can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network)
* [Kan jag aktivera Azure AD Domain Services i en Azure CSP-prenumeration (Cloud Solution Provider) ?](#can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription)
* [Kan jag aktivera Azure AD Domain Services i en federerad Azure AD-katalog? Jag synkroniserar inte lösenordshärman till Azure AD. Kan jag aktivera Azure AD Domain Services för den här katalogen?](#can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory)
* [Kan jag göra Azure AD Domain Services tillgängligt i flera virtuella nätverk i min prenumeration?](#can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription)
* [Kan jag aktivera Azure AD Domain Services med PowerShell?](#can-i-enable-azure-ad-domain-services-using-powershell)
* [Kan jag aktivera Azure AD Domain Services med hjälp av en Resource Manager-mall?](#can-i-enable-azure-ad-domain-services-using-a-resource-manager-template)
* [Kan jag lägga till domänkontrollanter i en hanterad Azure AD Domain Services-domän?](#can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain)
* [Kan gästanvändare som är inbjudna till min katalog använda Azure AD Domain Services?](#can-guest-users-invited-to-my-directory-use-azure-ad-domain-services)
* [Kan jag flytta en befintlig Azure AD Domain Services-hanterad domän till en annan prenumeration, resursgrupp, region eller virtuellt nätverk?](#can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network)
* [Innehåller Azure AD Domain Services alternativ med hög tillgänglighet?](#does-azure-ad-domain-services-include-high-availability-options)

### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Kan jag skapa flera hanterade domäner för en enda Azure AD-katalog?
Nej. Du kan bara skapa en enda hanterad domän som hanteras av Azure AD Domain Services för en enda Azure AD-katalog.

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>Kan jag aktivera Azure AD Domain Services i ett virtuellt klassiskt nätverk?
Klassiska virtuella nätverk stöds inte för nya distributioner. Befintliga hanterade domäner som distribueras i virtuella klassiska nätverk stöds fortfarande tills de dras tillbaka den 1 mars 2023. För befintliga distributioner kan du [migrera Azure AD Domain Services från den klassiska virtuella nätverksmodellen till Resource Manager](migrate-from-classic-vnet.md).

Mer information finns i det [officiella meddelandet om utfasning](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/).

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Kan jag aktivera Azure AD Domain Services i ett virtuellt Azure Resource Manager-nätverk?
Ja. Azure AD Domain Services kan aktiveras i ett virtuellt Azure Resource Manager-nätverk. Klassiska virtuella Azure-nätverk är inte längre tillgängliga när du skapar en hanterad domän.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Kan jag migrera min befintliga hanterade domän från ett virtuellt nätverk klassiskt till ett virtuellt resurshanterarenätverk?
Ja. Mer information finns i [Migrera Azure AD Domain Services från den klassiska virtuella nätverksmodellen till Resource Manager](migrate-from-classic-vnet.md).

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Kan jag aktivera Azure AD Domain Services i en Azure CSP-prenumeration (Cloud Solution Provider) ?
Ja. Mer information finns i [hur du aktiverar Azure AD Domain Services i Azure CSP-prenumerationer](csp.md).

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Kan jag aktivera Azure AD Domain Services i en federerad Azure AD-katalog? Jag synkroniserar inte lösenordshärman till Azure AD. Kan jag aktivera Azure AD Domain Services för den här katalogen?
Nej. För att autentisera användare via NTLM eller Kerberos behöver Azure AD Domain Services åtkomst till lösenordshashar av användarkonton. I en federerad katalog lagras inte lösenordsharare i Azure AD-katalogen. Därför fungerar inte Azure AD Domain Services med sådana Azure AD-kataloger.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Kan jag göra Azure AD Domain Services tillgängligt i flera virtuella nätverk i min prenumeration?
Själva tjänsten stöder inte direkt det här scenariot. Den hanterade domänen är bara tillgänglig i ett virtuellt nätverk åt gången. Du kan dock konfigurera anslutningen mellan flera virtuella nätverk för att exponera Azure AD Domain Services till andra virtuella nätverk. Mer information finns [i hur du ansluter virtuella nätverk i Azure med VPN-gateways](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) eller [virtuell nätverks peering](../virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Kan jag aktivera Azure AD Domain Services med PowerShell?
Ja. Mer information finns i [hur du aktiverar Azure AD Domain Services med PowerShell](powershell-create-instance.md).

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Kan jag aktivera Azure AD Domain Services med hjälp av en Resource Manager-mall?
Ja, du kan skapa en hanterad Azure AD Domain Services-domän med hjälp av en Resource Manager-mall. Ett tjänsthuvudnamn och Azure AD-grupp för administration måste skapas med Hjälp av Azure-portalen eller Azure PowerShell innan mallen distribueras. Mer information finns i [Skapa en Azure AD DS-hanterad domän med hjälp av en Azure Resource Manager-mall](template-create-instance.md). När du skapar en hanterad Azure AD-domäntjänst i Azure-portalen finns det också ett alternativ för att exportera mallen för användning med ytterligare distributioner.

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Kan jag lägga till domänkontrollanter i en hanterad Azure AD Domain Services-domän?
Nej. Domänen som tillhandahålls av Azure AD Domain Services är en hanterad domän. Du behöver inte etablera, konfigurera eller på annat sätt hantera domänkontrollanter för den här domänen. Dessa hanteringsaktiviteter tillhandahålls som en tjänst av Microsoft. Därför kan du inte lägga till ytterligare domänkontrollanter (skrivskyddade eller skrivskyddade) för den hanterade domänen.

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>Kan gästanvändare som är inbjudna till min katalog använda Azure AD Domain Services?
Nej. Gästanvändare som bjuds in till din Azure AD-katalog med hjälp av azure AD B2B-inbjudningsprocessen synkroniseras till din hanterade Azure AD Domain Services-domän. [Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) Lösenord för dessa användare lagras dock inte i din Azure AD-katalog. Azure AD Domain Services kan därför inte synkronisera NTLM- och Kerberos-hashar för dessa användare till den hanterade domänen. Sådana användare kan inte logga in eller ansluta datorer till den hanterade domänen.

### <a name="can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network"></a>Kan jag flytta en befintlig Azure AD Domain Services-hanterad domän till en annan prenumeration, resursgrupp, region eller virtuellt nätverk?
Nej. När du har skapat en hanterad Azure AD Domain Services-domän kan du inte flytta instansen till en annan resursgrupp, virtuellt nätverk, prenumeration osv. Var noga med att välja den lämpligaste prenumerationen, resursgruppen, regionen och det virtuella nätverket när du distribuerar Azure AD DS-instansen.

### <a name="does-azure-ad-domain-services-include-high-availability-options"></a>Innehåller Azure AD Domain Services alternativ med hög tillgänglighet?

Ja. Varje Azure AD Domain Services-hanterad domän innehåller två domänkontrollanter. Du hanterar eller ansluter inte till dessa domänkontrollanter, de är en del av den hanterade tjänsten. Om du distribuerar Azure AD-domäntjänster till en region som stöder tillgänglighetszoner distribueras domänkontrollanterna mellan zoner. I regioner som inte stöder tillgänglighetszoner fördelas domänkontrollanterna över tillgänglighetsuppsättningar. Du har inga konfigurationsalternativ eller hanteringskontroll över den här distributionen. Mer information finns i [Tillgänglighetsalternativ för virtuella datorer i Azure](../virtual-machines/windows/availability.md).

## <a name="administration-and-operations"></a>Administration och drift

* [Kan jag ansluta till domänkontrollanten för min hanterade domän med Fjärrskrivbord?](#can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop)
* [Jag har aktiverat Azure AD Domain Services. Vilket användarkonto använder jag för att ansluta datorer till den här domänen?](#ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain)
* [Har jag domänadministratörsbehörighet för den hanterade domänen som tillhandahålls av Azure AD Domain Services?](#do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services)
* [Kan jag ändra gruppmedlemskap med hjälp av LDAP eller andra AD-administrationsverktyg på hanterade domäner?](#can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains)
* [Hur lång tid tar det innan ändringar jag gör i min Azure AD-katalog ska vara synliga i min hanterade domän?](#how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain)
* [Kan jag utöka schemat för den hanterade domänen som tillhandahålls av Azure AD Domain Services?](#can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services)
* [Kan jag ändra eller lägga till DNS-poster i min hanterade domän?](#can-i-modify-or-add-dns-records-in-my-managed-domain)
* [Vad är principen för lösenordslivslängd på en hanterad domän?](#what-is-the-password-lifetime-policy-on-a-managed-domain)
* [Tillhandahåller Azure AD Domain Services skydd för AD-kontoutelåsning?](#does-azure-ad-domain-services-provide-ad-account-lockout-protection)

### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Kan jag ansluta till domänkontrollanten för min hanterade domän med Fjärrskrivbord?
Nej. Du har inte behörighet att ansluta till domänkontrollanter för den hanterade domänen med Fjärrskrivbord. Medlemmar i gruppen *AAD DC-administratörer* kan administrera den hanterade domänen med hjälp av AD-administrationsverktyg som ADAC (Active Directory Administration Center) eller AD PowerShell. Dessa verktyg installeras med hjälp av funktionen Administrationsverktyg för *fjärrserver* på en Windows-server som är ansluten till den hanterade domänen. Mer information finns i [Skapa en virtuell hanterings-dator för att konfigurera och administrera en hanterad Azure AD Domain Services-domän](tutorial-create-management-vm.md).

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Jag har aktiverat Azure AD Domain Services. Vilket användarkonto använder jag för att ansluta datorer till den här domänen?
Alla användarkonton som ingår i Azure AD DS-hanterade domänen kan ansluta till en virtuell dator. Medlemmar i gruppen *AAD DC-administratörer* beviljas fjärrskrivbordsåtkomst till datorer som har anslutits till den hanterade domänen.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Har jag domänadministratörsbehörighet för den hanterade domänen som tillhandahålls av Azure AD Domain Services?
Nej. Du beviljas inte administratörsbehörighet för den hanterade domänen. *Domänadministratör* och företagsadministratörsbehörighet är inte tillgängliga för dig att använda inom domänen. *Enterprise Administrator* Medlemmar i domänadministratören eller företagsadministratörsgrupperna i den lokala Active Directory beviljas inte heller domän-/företagsadministratörsbehörighet på den hanterade domänen.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Kan jag ändra gruppmedlemskap med hjälp av LDAP eller andra AD-administrationsverktyg på hanterade domäner?
Användare och grupper som synkroniseras från Azure Active Directory till Azure AD Domain Services kan inte ändras eftersom deras ursprungskälla är Azure Active Directory. Alla användare eller grupper som har sitt ursprung i den hanterade domänen kan ändras.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Hur lång tid tar det innan ändringar jag gör i min Azure AD-katalog ska vara synliga i min hanterade domän?
Ändringar som görs i din Azure AD-katalog med Azure AD-användargränssnittet eller PowerShell synkroniseras automatiskt med din hanterade domän. Den här synkroniseringsprocessen körs i bakgrunden. Det finns ingen definierad tidsperiod för den här synkroniseringen för att slutföra alla objektändringar.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Kan jag utöka schemat för den hanterade domänen som tillhandahålls av Azure AD Domain Services?
Nej. Schemat administreras av Microsoft för den hanterade domänen. Schematillägg stöds inte av Azure AD Domain Services.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Kan jag ändra eller lägga till DNS-poster i min hanterade domän?
Ja. Medlemmar i gruppen *AAD DC-administratörer* beviljas *DNS-administratörsbehörighet* för att ändra DNS-poster i den hanterade domänen. Dessa användare kan använda DNS-hanterarens konsol på en dator som kör Windows Server som är ansluten till den hanterade domänen för att hantera DNS. Om du vill använda DNS-hanterarens konsol installerar du *DNS Server Tools*, som ingår i valfri funktion för *fjärrserveradministrationsverktyg* på servern. Mer information finns [i Administrera DNS i en hanterad Domändomän](manage-dns.md)för Azure AD Domain Services .

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Vad är principen för lösenordslivslängd på en hanterad domän?
Standardlivstiden för lösenord på en hanterad Azure AD-domän (90 dagar). Den här lösenordslivstiden synkroniseras inte med lösenordets livstid som konfigurerats i Azure AD. Därför kan du ha en situation där användarnas lösenord upphör att gälla i din hanterade domän, men fortfarande är giltiga i Azure AD. I sådana fall måste användarna ändra sitt lösenord i Azure AD och det nya lösenordet synkroniseras med din hanterade domän. Dessutom synkroniseras *inte attributen lösenord-inte-upphör att gälla* och attribut för *användaren-måste-ändra-lösenord-vid-nästa-inloggning* för användarkonton inte till din hanterade domän.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Tillhandahåller Azure AD Domain Services skydd för AD-kontoutelåsning?
Ja. Fem ogiltiga lösenordsförsök inom 2 minuter på den hanterade domänen gör att ett användarkonto är utelåst i 30 minuter. Efter 30 minuter låses användarkontot upp automatiskt. Ogiltiga lösenordsförsök på den hanterade domänen låser inte användarkontot i Azure AD. Användarkontot är endast utelåst i din hanterade Azure AD Domain Services-domän. Mer information finns i [Principer för lösenords- och kontoutelåsning på hanterade domäner](password-policy.md).

## <a name="billing-and-availability"></a>Fakturering och tillgänglighet

* [Är Azure AD Domain Services en betald tjänst?](#is-azure-ad-domain-services-a-paid-service)
* [Finns det en gratis provperiod för tjänsten?](#is-there-a-free-trial-for-the-service)
* [Kan jag pausa en hanterad Azure AD Domain Services-domän?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Kan jag redundan Azure AD Domain Services till en annan region för en DR-händelse?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Kan jag få Azure AD Domain Services som en del av Enterprise Mobility Suite (EMS)? Behöver jag Azure AD Premium för att kunna använda Azure AD Domain Services?](#can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event)
* [Vilka Azure-regioner är tjänsten tillgänglig i?](#can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services)

### <a name="is-azure-ad-domain-services-a-paid-service"></a>Är Azure AD Domain Services en betald tjänst?
Ja. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/active-directory-ds/).

### <a name="is-there-a-free-trial-for-the-service"></a>Finns det en gratis provperiod för tjänsten?
Azure AD Domain Services ingår i den kostnadsfria utvärderingsversionen för Azure. Du kan registrera dig för en [kostnadsfri en månads utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Kan jag pausa en hanterad Azure AD Domain Services-domän?
Nej. När du har aktiverat en hanterad Azure AD Domain Services-domän är tjänsten tillgänglig i det valda virtuella nätverket tills du tar bort den hanterade domänen. Det går inte att pausa tjänsten. Faktureringen fortsätter varje timme tills du tar bort den hanterade domänen.

### <a name="can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>Kan jag redundan Azure AD Domain Services till en annan region för en DR-händelse?
Nej. Azure AD Domain Services tillhandahåller för närvarande inte en geo-redundant distributionsmodell. Det är begränsat till ett enda virtuellt nätverk i en Azure-region. Om du vill använda flera Azure-regioner måste du köra dina Active Directory-domänkontrollanter på virtuella Azure IaaS-datorer. Om du vill ha arkitekturvägledning finns i [Utöka din lokala Active Directory-domän till Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain).

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Kan jag få Azure AD Domain Services som en del av Enterprise Mobility Suite (EMS)? Behöver jag Azure AD Premium för att kunna använda Azure AD Domain Services?
Nej. Azure AD Domain Services är en Azure-tjänst som gäller för varje betalning och ingår inte i EMS. Azure AD Domain Services kan användas med alla versioner av Azure AD (Free och Premium). Du faktureras per timme, beroende på användning.

### <a name="what-azure-regions-is-the-service-available-in"></a>Vilka Azure-regioner är tjänsten tillgänglig i?
Gå till sidan [Azure Services efter region](https://azure.microsoft.com/regions/#services/) för att se en lista över De Azure-regioner där Azure AD Domain Services är tillgängligt.

## <a name="troubleshooting"></a>Felsökning

Mer information om hur du konfigurerar eller administrerar Azure AD Domain Services finns i [felsökningsguiden.](troubleshoot.md)

## <a name="next-steps"></a>Nästa steg

Mer information om Azure AD Domain Services finns i [Vad är Azure Active Directory Domain Services?](overview.md)

Information om hur du kommer igång finns i [Skapa och konfigurera en Azure Active Directory Domain Services-instans](tutorial-create-instance.md).
