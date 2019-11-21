---
title: Hanterings begrepp för Azure AD Domain Services | Microsoft Docs
description: Lär dig mer om hur du administrerar en Azure Active Directory Domain Services hanterad domän och hur användar konton och lösen ord fungerar
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: iainfou
ms.openlocfilehash: a5a08bddb53afe8f698b0d96621cc116ee866070
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74209176"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Hanterings begrepp för användar konton, lösen ord och administration i Azure Active Directory Domain Services

När du skapar och kör en Azure Active Directory Domain Services (AD DS)-hanterad domän, finns det vissa skillnader i beteende jämfört med en traditionell lokal AD DS-miljö. Du använder samma administrativa verktyg i Azure AD DS som en självhanterad domän, men du kan inte komma åt domän kontrol Lanterna direkt. Det finns också vissa skillnader i beteendet för lösen ords principer och lösen ords-hashar beroende på källan till det användar konto som skapas.

Den här konceptuella artikeln beskriver hur du administrerar en Azure AD DS-hanterad domän och olika beteenden för användar konton beroende på hur de skapas.

## <a name="domain-management"></a>Domän hantering

I Azure AD DS är domän kontrol Lanterna (DCs) som innehåller alla resurser, t. ex. användare och grupper, autentiseringsuppgifter och principer, en del av den hanterade tjänsten. För redundans skapas två domänkontrollanter som en del av en Azure AD DS-hanterad domän. Du kan inte logga in på dessa domänkontrollanter för att utföra hanterings uppgifter. I stället skapar du en virtuell hanterings dator som är ansluten till den hanterade Azure AD DS-domänen och installerar sedan dina vanliga hanterings verktyg för AD DS. Du kan till exempel använda snapin-modulerna Active Directory Administrationscenter eller Microsoft Management Console (MMC) som DNS eller grupprincip objekt.

## <a name="user-account-creation"></a>Skapa användar konto

Användar konton kan skapas i Azure AD DS på flera olika sätt. De flesta användar konton synkroniseras i från Azure AD, vilket även kan omfatta användar konto som synkroniseras från en lokal AD DS-miljö. Du kan också skapa konton manuellt direkt i Azure AD DS. Vissa funktioner, t. ex. inledande Lösenordssynkronisering eller lösen ords princip, beter sig på olika sätt beroende på hur och var användar konton skapas.

* Användar kontot kan synkroniseras i från Azure AD. Detta inkluderar endast molnbaserade användar konton som skapas direkt i Azure AD och hybrid användar konton som synkroniseras från en lokal AD DS-miljö med hjälp av Azure AD Connect.
    * Majoriteten av användar kontona i Azure AD DS skapas genom synkroniseringsprocessen från Azure AD.
* Användar kontot kan skapas manuellt i en Azure AD DS-hanterad domän och finns inte i Azure AD.
    * Om du behöver skapa tjänst konton för program som bara körs i Azure AD DS kan du skapa dem manuellt i den hanterade domänen. Eftersom synkroniseringen är enkelriktad från Azure AD, synkroniseras inte användar konton som skapats i Azure AD DS tillbaka till Azure AD.

## <a name="password-policy"></a>Lösen ords princip

Azure AD DS innehåller en standard lösen ords princip som definierar inställningar för saker som konto utelåsning, högsta ålder för lösen ord och lösen ords komplexitet. Inställningar som konto utelåsnings princip gäller för alla användare i Azure AD DS, oavsett hur användaren skapades enligt beskrivningen i föregående avsnitt. Några inställningar, t. ex. minsta längd på lösen ord och lösen ords komplexitet, gäller endast för användare som skapats direkt i Azure AD DS.

Du kan skapa egna lösen ords principer om du vill åsidosätta standard principen i Azure AD DS. De här anpassade principerna kan sedan tillämpas på vissa grupper av användare efter behov.

Mer information om skillnaderna i hur lösen ords principer tillämpas, beroende på källan för att skapa användare, finns i [principer för lösen ords-och konto utelåsning på hanterade domäner][password-policy].

## <a name="password-hashes"></a>Lösenords-hashvärden

För att autentisera användare på den hanterade domänen behöver Azure AD DS lösen ords-hashar i ett format som är lämpligt för NT LAN Manager (NTLM) och Kerberos-autentisering. Azure AD genererar eller lagrar inte lösen ordets hash-värden i det format som krävs för NTLM-eller Kerberos-autentisering förrän du aktiverar Azure AD DS för din klient. Av säkerhets skäl lagrar Azure AD inte heller lösen ords referenser i klartext-format. Därför kan inte Azure AD automatiskt generera dessa NTLM-eller Kerberos-hashvärden utifrån användarnas befintliga autentiseringsuppgifter.

För endast molnbaserade användar konton måste användare ändra sina lösen ord innan de kan använda Azure AD DS. Den här processen för lösen ords ändring gör att lösen ords hashar för Kerberos-och NTLM-autentisering skapas och lagras i Azure AD.

För användare som synkroniseras från en lokal AD DS-miljö med hjälp av Azure AD Connect [aktiverar du synkronisering av lösen ords-hashar][hybrid-phs].

> [!IMPORTANT]
> Azure AD Connect synkroniserar bara äldre lösen ords-hashar när du aktiverar Azure AD DS för Azure AD-klienten. Äldre hashvärden för lösen ord används inte om du bara använder Azure AD Connect för att synkronisera en lokal AD DS-miljö med Azure AD.
>
> Om dina äldre program inte använder NTLM-autentisering eller enkla LDAP-bindningar, rekommenderar vi att du inaktiverar hash-synkronisering av NTLM-lösenord för Azure AD DS. Mer information finns i [inaktivera svaga chiffersviter och hash-synkronisering av NTLM-autentiseringsuppgifter][secure-domain].

När det är korrekt konfigurerat lagras de användbara lösen ords hasharna i den hanterade domänen i Azure AD DS. Om du tar bort den hanterade Azure AD DS-domänen, raderas även alla lösen ords-hashar som lagras i den punkten. Det går inte att återanvända synkroniserad autentiseringsinformation i Azure AD om du senare skapar en Azure AD DS-hanterad domän. du måste konfigurera om lösen ordets hash-synkronisering för att lagra lösen ordets hash-meddelanden igen. Tidigare domänanslutna virtuella datorer eller användare kan inte omedelbart autentisera – Azure AD behöver generera och lagra lösen ordets hash-värden i den nya Azure AD DS-hanterade domänen. Mer information finns i [synkronisering av lösen ords-hash för Azure AD DS och Azure AD Connect][azure-ad-password-sync].

## <a name="forests-and-trusts"></a>Skogar och förtroenden

En *skog* är en logisk konstruktion som används av Active Directory Domain Services (AD DS) för att gruppera en eller flera *domäner*. Domänerna lagrar sedan objekt för användare eller grupper och tillhandahåller Authentication Services.

I Azure AD DS innehåller skogen bara en domän. Lokala AD DS-skogar innehåller ofta många domäner. I stora organisationer, särskilt efter sammanslagning och förvärv, kan du få flera lokala skogar som var och en innehåller flera domäner.

Som standard skapas en Azure AD DS-hanterad domän som en *användar* skog. Den här typen av skog synkroniserar alla objekt från Azure AD, inklusive alla användar konton som skapats i en lokal AD DS-miljö. Användar konton kan autentiseras direkt mot Azure AD DS-hanterad domän, t. ex. för att logga in på en domänansluten virtuell dator. En användar skog fungerar när lösen ordets hash-värden kan synkroniseras och användarna inte använder exklusiva inloggnings metoder som smartkort-autentisering.

I en Azure AD DS- *resurs* kan användare autentiseras över ett enkelriktat skogs *förtroende* från sina lokala AD DS. Med den här metoden synkroniseras inte användar objekt och lösen ordets hash-värden till Azure AD DS. Användar objekt och autentiseringsuppgifter finns bara i den lokala AD DS. Med den här metoden kan företag hantera resurser och programplattformar i Azure som är beroende av klassisk autentisering, t. ex. LDAP, Kerberos eller NTLM, men eventuella autentiseringsproblem eller problem tas bort. Azure AD DS resurs skogar är för närvarande en för hands version.

Mer information om skogs typer i Azure AD DS finns i [Vad är resurs skogar?][concepts-forest] och [Hur fungerar skogs förtroenden i Azure AD DS?][concepts-trust]

## <a name="next-steps"></a>Nästa steg

Kom igång genom att [skapa en Azure AD DS-hanterad domän][create-instance].

<!-- INTERNAL LINKS -->
[password-policy]: password-policy.md
[hybrid-phs]: tutorial-configure-password-hash-sync.md#enable-synchronization-of-password-hashes
[secure-domain]: secure-your-domain.md
[azure-ad-password-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[create-instance]: tutorial-create-instance.md
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
