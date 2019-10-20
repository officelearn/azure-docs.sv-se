---
title: Vanliga frågor och svar om Azure AD Domain Services | Microsoft Docs
description: Läs och förstå några av de vanliga frågorna kring konfiguration, administration och tillgänglighet för Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: iainfou
ms.openlocfilehash: 4add98ae092359c6f070bfc93b69257894f44e48
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596422"
---
# <a name="frequently-asked-questions-faqs"></a>Vanliga frågor och svar

På den här sidan besvaras vanliga frågor om Azure Active Directory Domain Services.

## <a name="configuration"></a>Konfiguration

* [Kan jag skapa flera hanterade domäner för en enda Azure AD-katalog?](#can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory)
* [Kan jag aktivera Azure AD Domain Services i ett klassiskt virtuellt nätverk?](#can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network)
* [Kan jag aktivera Azure AD Domain Services i ett Azure Resource Manager virtuellt nätverk?](#can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network)
* [Kan jag migrera min befintliga hanterade domän från ett klassiskt virtuellt nätverk till ett virtuellt Resource Manager-nätverk?](#can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network)
* [Kan jag aktivera Azure AD Domain Services i en Azure CSP-prenumeration (Cloud Solution Provider)?](#can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription)
* [Kan jag aktivera Azure AD Domain Services i en federerad Azure AD-katalog? Jag synkroniserar inte lösen ordets hash-värden till Azure AD. Kan jag aktivera Azure AD Domain Services för den här katalogen?](#can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory)
* [Kan jag göra Azure AD Domain Services tillgängliga i flera virtuella nätverk i min prenumeration?](#can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription)
* [Kan jag aktivera Azure AD Domain Services med PowerShell?](#can-i-enable-azure-ad-domain-services-using-powershell)
* [Kan jag aktivera Azure AD Domain Services med hjälp av en Resource Manager-mall?](#can-i-enable-azure-ad-domain-services-using-a-resource-manager-template)
* [Kan jag lägga till domänkontrollanter till en Azure AD Domain Services hanterad domän?](#can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain)
* [Kan gäst användare som bjuds in till min katalog använda Azure AD Domain Services?](#can-guest-users-invited-to-my-directory-use-azure-ad-domain-services)
* [Kan jag flytta en befintlig Azure AD Domain Services hanterad domän till en annan prenumeration, resurs grupp, region eller virtuellt nätverk?](#can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network)

### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Kan jag skapa flera hanterade domäner för en enda Azure AD-katalog?
Nej. Det går bara att skapa en enskild hanterad domän som hanteras av Azure AD Domain Services för en enda Azure AD-katalog.

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>Kan jag aktivera Azure AD Domain Services i ett klassiskt virtuellt nätverk?
Klassiska virtuella nätverk stöds inte för nya distributioner. Befintliga hanterade domäner som distribueras i klassiska virtuella nätverk fortsätter att stödjas.

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Kan jag aktivera Azure AD Domain Services i ett Azure Resource Manager virtuellt nätverk?
Ja. Azure AD Domain Services kan aktive ras i ett Azure Resource Manager virtuellt nätverk. Klassiska virtuella Azure-nätverk stöds inte längre när du skapar en ny hanterad domän.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Kan jag migrera min befintliga hanterade domän från ett klassiskt virtuellt nätverk till ett virtuellt Resource Manager-nätverk?
Ja, den här funktionen är i för hands version. Mer information finns i [migrera Azure AD Domain Services från den klassiska virtuella nätverks modellen till Resource Manager (för hands version)] [migrera-från-klassisk-vnet.md]

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Kan jag aktivera Azure AD Domain Services i en Azure CSP-prenumeration (Cloud Solution Provider)?
Ja. Mer information finns i [så här aktiverar du Azure AD Domain Services i Azure CSP-prenumerationer](csp.md).

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Kan jag aktivera Azure AD Domain Services i en federerad Azure AD-katalog? Jag synkroniserar inte lösen ordets hash-värden till Azure AD. Kan jag aktivera Azure AD Domain Services för den här katalogen?
Nej. För att autentisera användare via NTLM eller Kerberos behöver Azure AD Domain Services åtkomst till hashar för lösen ord för användar konton. I en federerad katalog lagras inte lösen ordets hash-värden i Azure AD-katalogen. Därför fungerar Azure AD Domain Services inte med sådana Azure AD-kataloger.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Kan jag göra Azure AD Domain Services tillgängliga i flera virtuella nätverk i min prenumeration?
Själva tjänsten har inte direkt stöd för det här scenariot. Din hanterade domän är bara tillgänglig i ett virtuellt nätverk i taget. Du kan dock konfigurera anslutningar mellan flera virtuella nätverk för att exponera Azure AD Domain Services till andra virtuella nätverk. Mer information finns i [så här ansluter du virtuella nätverk i Azure med hjälp av VPN-gatewayer](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) eller [peering för virtuella nätverk](../virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Kan jag aktivera Azure AD Domain Services med PowerShell?
Ja. Mer information finns i [så här aktiverar du Azure AD Domain Services med hjälp av PowerShell](powershell-create-instance.md).

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Kan jag aktivera Azure AD Domain Services med hjälp av en Resource Manager-mall?
Nej, det är för närvarande inte möjligt att aktivera Azure AD Domain Services med hjälp av en mall. En skriptad metod finns i [så här aktiverar du Azure AD Domain Services med hjälp av PowerShell](powershell-create-instance.md).

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Kan jag lägga till domänkontrollanter till en Azure AD Domain Services hanterad domän?
Nej. Den domän som tillhandahålls av Azure AD Domain Services är en hanterad domän. Du behöver inte etablera, konfigurera eller på annat sätt hantera domänkontrollanter för den här domänen. Dessa hanterings aktiviteter tillhandahålls som en tjänst av Microsoft. Därför kan du inte lägga till ytterligare domänkontrollanter (Läs-och skriv skydd) för den hanterade domänen.

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>Kan gäst användare som bjuds in till min katalog använda Azure AD Domain Services?
Nej. Gäst användare som bjudits in till din Azure AD-katalog med [Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) -beställnings processen synkroniseras med din Azure AD Domain Services hanterade domänen. Lösen ord för dessa användare lagras dock inte i Azure AD-katalogen. Azure AD Domain Services har därför inget sätt att synkronisera NTLM-och Kerberos-hashvärden för dessa användare till din hanterade domän. Sådana användare kan inte logga in eller ansluta datorer till den hanterade domänen.

### <a name="can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network"></a>Kan jag flytta en befintlig Azure AD Domain Services hanterad domän till en annan prenumeration, resurs grupp, region eller virtuellt nätverk?
Nej. När du har skapat en Azure AD Domain Services hanterad domän kan du inte flytta instansen till en annan resurs grupp, ett virtuellt nätverk, en prenumeration osv. Var noga med att välja den lämpligaste prenumerationen, resurs gruppen, regionen och det virtuella nätverket när du distribuerar Azure AD DS-instansen.

## <a name="administration-and-operations"></a>Administration och åtgärder

* [Kan jag ansluta till domänkontrollanten för min hanterade domän med hjälp av fjärr skrivbord?](#can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop)
* [Jag har aktiverat Azure AD Domain Services. Vilket användar konto använder jag för att ansluta till domän datorer till den här domänen?](#ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain)
* [Har jag domän administratörs behörighet för den hanterade domänen som tillhandahålls av Azure AD Domain Services?](#do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services)
* [Kan jag ändra grupp medlemskap med hjälp av LDAP eller andra administrations verktyg för AD i hanterade domäner?](#can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains)
* [Hur lång tid tar det innan ändringar jag gör i Azure AD-katalogen visas i min hanterade domän?](#how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain)
* [Kan jag utöka schemat för den hanterade domänen som tillhandahålls av Azure AD Domain Services?](#can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services)
* [Kan jag ändra eller lägga till DNS-poster i min hanterade domän?](#can-i-modify-or-add-dns-records-in-my-managed-domain)
* [Vad är en princip för lösen ords livs längd på en hanterad domän?](#what-is-the-password-lifetime-policy-on-a-managed-domain)
* [Tillhandahåller Azure AD Domain Services AD-konto utelåsnings skydd?](#does-azure-ad-domain-services-provide-ad-account-lockout-protection)

### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Kan jag ansluta till domänkontrollanten för min hanterade domän med hjälp av fjärr skrivbord?
Nej. Du har inte behörighet att ansluta till domänkontrollanter för den hanterade domänen med hjälp av fjärr skrivbord. Medlemmar i gruppen *AAD DC-administratörer* kan administrera den hanterade domänen med hjälp av administrations verktyg för AD, till exempel Active Directory administrations Center (ADAC) eller AD PowerShell. De här verktygen installeras med hjälp av funktionen *verktyg för fjärrserveradministration* på en Windows Server som är ansluten till den hanterade domänen. Mer information finns i [skapa en virtuell hanterings dator för att konfigurera och administrera en Azure AD Domain Services hanterad domän](tutorial-create-management-vm.md).

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Jag har aktiverat Azure AD Domain Services. Vilket användar konto använder jag för att ansluta till domän datorer till den här domänen?
Medlemmar i administratörs gruppen *AAD DC-administratörer* kan ansluta datorer till domän. Medlemmar i den här gruppen beviljas dessutom fjärr skrivbords åtkomst till datorer som har anslutits till domänen.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Har jag domän administratörs behörighet för den hanterade domänen som tillhandahålls av Azure AD Domain Services?
Nej. Du har inte beviljats administratörs behörighet för den hanterade domänen. Behörigheterna *domän administratör* och *företags administratör* är inte tillgängliga för dig i domänen. Medlemmar i grupperna domän administratör eller företags administratör i din lokala Active Directory beviljas inte heller domän-/företags administratörs behörighet på den hanterade domänen.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Kan jag ändra grupp medlemskap med hjälp av LDAP eller andra administrations verktyg för AD i hanterade domäner?
Nej. Det går inte att ändra grupp medlemskap på domäner som hanteras av Azure AD Domain Services. Samma gäller för användarattribut. Du kan ändra grupp medlemskap eller användarattribut antingen i Azure AD eller i din lokala domän. Ändringarna synkroniseras automatiskt till Azure AD Domain Services.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Hur lång tid tar det innan ändringar jag gör i Azure AD-katalogen visas i min hanterade domän?
Ändringar som görs i Azure AD-katalogen med antingen Azure AD-ANVÄNDARGRÄNSSNITTET eller PowerShell synkroniseras automatiskt till din hanterade domän. Den här synkroniseringsprocessen körs i bakgrunden. Det finns ingen definierad tids period för den här synkroniseringen att slutföra alla objekt ändringar.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Kan jag utöka schemat för den hanterade domänen som tillhandahålls av Azure AD Domain Services?
Nej. Schemat administreras av Microsoft för den hanterade domänen. Schema utökningar stöds inte av Azure AD Domain Services.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Kan jag ändra eller lägga till DNS-poster i min hanterade domän?
Ja. Medlemmar i gruppen *AAD DC-administratörer* har behörighet som *DNS-administratör* för att ändra DNS-poster i den hanterade domänen. Dessa användare kan använda DNS-hanterarens konsol på en dator som kör Windows Server som är ansluten till den hanterade domänen för att hantera DNS. Om du vill använda DNS-hanterarens konsol installerar du *verktyg för DNS-Server*, som är en del av den *verktyg för fjärrserveradministration* valfria funktionen på servern. Mer information finns i [Administrera DNS i en Azure AD Domain Services hanterad domän](manage-dns.md).

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Vad är en princip för lösen ords livs längd på en hanterad domän?
Standard livstiden för lösen ord för en Azure AD Domain Services hanterad domän är 90 dagar. Lösen ordets livs längd är inte synkroniserad med lösen ordets livstid som kon figurer ATS i Azure AD. Därför kan du ha en situation där användarens lösen ord upphör att gälla i din hanterade domän, men fortfarande är giltiga i Azure AD. I sådana fall måste användarna ändra sina lösen ord i Azure AD och det nya lösen ordet synkroniseras med den hanterade domänen. *Lösen ordet – fungerar inte, upphör att gälla* och *användaren måste byta lösen ord vid nästa inloggning* för användar konton synkroniseras heller inte till din hanterade domän.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Tillhandahåller Azure AD Domain Services AD-konto utelåsnings skydd?
Ja. Fem ogiltiga lösen ords försök inom 2 minuter på den hanterade domänen gör att ett användar konto blir utelåst i 30 minuter. Efter 30 minuter låses användar kontot automatiskt upp. Ogiltiga lösen ords försök i den hanterade domänen låser inte användar kontot i Azure AD. Användar kontot är bara utelåst i din Azure AD Domain Services hanterade domän. Mer information finns i [principer för lösen ord och konto utelåsning på hanterade domäner](password-policy.md).

## <a name="billing-and-availability"></a>Fakturering och tillgänglighet

* [Är Azure AD Domain Services en betald tjänst?](#is-azure-ad-domain-services-a-paid-service)
* [Finns det en kostnads fri utvärderings version av tjänsten?](#is-there-a-free-trial-for-the-service)
* [Kan jag pausa en Azure AD Domain Services hanterad domän?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Kan jag redundansväxla Azure AD Domain Services till en annan region för en DR-händelse?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Kan jag få Azure AD Domain Services som en del av EMS (Enterprise Mobility Suite)? Behöver jag Azure AD Premium använda Azure AD Domain Services?](#can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event)
* [Vilka Azure-regioner finns tjänsten i?](#can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services)

### <a name="is-azure-ad-domain-services-a-paid-service"></a>Är Azure AD Domain Services en betald tjänst?
Ja. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/active-directory-ds/).

### <a name="is-there-a-free-trial-for-the-service"></a>Finns det en kostnads fri utvärderings version av tjänsten?
Azure AD Domain Services ingår i den kostnads fria utvärderings versionen av Azure. Du kan registrera dig för en [kostnads fri utvärderings version av Azure på en månad](https://azure.microsoft.com/pricing/free-trial/).

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Kan jag pausa en Azure AD Domain Services hanterad domän?
Nej. När du har aktiverat en Azure AD Domain Services hanterad domän är tjänsten tillgänglig i det valda virtuella nätverket tills du tar bort den hanterade domänen. Det finns inget sätt att pausa tjänsten. Faktureringen fortsätter per timme tills du tar bort den hanterade domänen.

### <a name="can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>Kan jag redundansväxla Azure AD Domain Services till en annan region för en DR-händelse?
Nej. Azure AD Domain Services tillhandahåller för närvarande ingen Geo-redundant distributions modell. It'is begränsat till ett enda virtuellt nätverk i en Azure-region. Om du vill använda flera Azure-regioner måste du köra dina Active Directory-domän kontrollanter på virtuella Azure IaaS-datorer. Vägledning för arkitektur finns [här](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain).

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Kan jag få Azure AD Domain Services som en del av EMS (Enterprise Mobility Suite)? Behöver jag Azure AD Premium använda Azure AD Domain Services?
Nej. Azure AD Domain Services är en Azure-tjänst enligt principen betala per användning och ingår inte i EMS. Azure AD Domain Services kan användas med alla utgåvor av Azure AD (kostnads fri och Premium). Du faktureras per timme, beroende på användning.

### <a name="what-azure-regions-is-the-service-available-in"></a>Vilka Azure-regioner finns tjänsten i?
På sidan [Azure-tjänster per region](https://azure.microsoft.com/regions/#services/) kan du se en lista över de Azure-regioner där Azure AD Domain Services är tillgängligt.

## <a name="troubleshooting"></a>Felsöka

Läs [fel söknings guiden](troubleshoot.md) för lösningar på vanliga problem med att konfigurera eller administrera Azure AD Domain Services.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure AD Domain Services finns i [Vad är Azure Active Directory Domain Services?](overview.md).

Information om hur du kommer igång finns i [skapa och konfigurera en Azure Active Directory Domain Services-instans](tutorial-create-instance.md).
