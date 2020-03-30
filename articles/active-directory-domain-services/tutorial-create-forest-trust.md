---
title: Självstudiekurs - Skapa ett skogsförtroende i Azure AD Domain Services | Microsoft-dokument
description: Lär dig hur du skapar en envägs utgående skog till en lokal AD DS-domän i Azure-portalen för Azure AD Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: 5620d1cdc7dc71bdac17057b9a13a74150b12d5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612512"
---
# <a name="tutorial-create-an-outbound-forest-trust-to-an-on-premises-domain-in-azure-active-directory-domain-services-preview"></a>Självstudiekurs: Skapa ett utgående skogsförtroende till en lokal domän i Azure Active Directory Domain Services (förhandsversion)

I miljöer där du inte kan synkronisera lösenordsharhar, eller om du har användare som uteslutande loggar in med smartkort så att de inte känner till sitt lösenord, kan du använda en resursskog i Azure Active Directory Domain Services (AD DS). En resursskog använder ett enkelriktad utgående förtroende från Azure AD DS till en eller flera lokala AD DS-miljöer. Med den här förtroenderelationen kan användare, program och datorer autentisera mot en lokal domän från azure AD DS-hanterade domänen. Azure AD DS-resursskogar är för närvarande i förhandsversion.

![Diagram över skogsförtroende från Azure AD DS till lokalt AD DS](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Konfigurera DNS i en lokal AD DS-miljö för att stödja Azure AD DS-anslutning
> * Skapa ett enkelriktad inkommande skogsförtroende i en lokal AD DS-miljö
> * Skapa ett envägs utgående skogsförtroende i Azure AD DS
> * Testa och verifiera förtroenderelationen för autentisering och resursåtkomst

Om du inte har en Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du följande resurser och privilegier:

* En aktiv Azure-prenumeration.
    * Om du inte har en Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* En Azure Active Directory-klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog med endast molnet.
    * Om det behövs [skapar du en Azure Active Directory-klientorganisation][create-azure-ad-tenant] eller [associerar en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En hanterad Azure Active Directory Domain Services-domän som skapats med hjälp av en resursskog och konfigurerad i din Azure AD-klientorganisation.
    * Om det behövs [kan du skapa och konfigurera en Azure Active Directory Domain Services-instans][create-azure-ad-ds-instance-advanced].
    
    > [!IMPORTANT]
    > Se till att du skapar en Azure AD DS-hanterad domän med hjälp av en *resursskog.* Standardalternativet skapar en *användarskog.* Endast resursskogar kan skapa förtroenden för AD DS-miljöer på prem. Du måste också använda ett minimum av *Enterprise* SKU för din hanterade domän. Om det behövs [ändrar du SKU för en Azure AD DS-hanterad domän][howto-change-sku].

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

I den här självstudien skapar och konfigurerar du det utgående skogsförtroendet från Azure AD DS med Azure-portalen. Kom igång genom att logga in på [Azure-portalen](https://portal.azure.com).

## <a name="networking-considerations"></a>Nätverksöverväganden

Det virtuella nätverket som är värd för Azure AD DS-resursskogen behöver nätverksanslutning till din lokala Active Directory. Program och tjänster behöver också nätverksanslutning till det virtuella nätverket som är värd för Azure AD DS-resursskogen. Nätverksanslutning till Azure AD DS-resursskogen måste alltid vara på och stabil annars kan användare misslyckas med att autentisera eller komma åt resurser.

Innan du konfigurerar ett skogsförtroende i Azure AD DS kontrollerar du att nätverket mellan Azure och den lokala miljön uppfyller följande krav:

* Använd privata IP-adresser. Lita inte på DHCP med dynamisk IP-adresstilldelning.
* Undvik överlappande IP-adressutrymmen så att virtuell nätverks peering och routning kan kommunicera mellan Azure och lokala.
* Ett virtuellt Azure-nätverk behöver ett gateway-undernät för att konfigurera en S2S-VPN- eller ExpressRoute-anslutning (Site-to-Site)
* Skapa undernät med tillräckligt många IP-adresser för att stödja ditt scenario.
* Kontrollera att Azure AD DS har ett eget undernät, dela inte det här virtuella nätverksundernätet med virtuella program-datorer och tjänster.
* Peered virtuella nätverk är inte transitiva.
    * Azure virtuella nätverks peerings måste skapas mellan alla virtuella nätverk som du vill använda Azure AD DS resursskog förtroende till den lokala AD DS-miljön.
* Tillhandahålla kontinuerlig nätverksanslutning till din lokala Active Directory-skog. Använd inte anslutningar på begäran.
* Kontrollera att det finns en kontinuerlig namnmatchning (DNS) mellan ditt Azure AD DS-resursskogsnamn och ditt lokala Active Directory-skogsnamn.

## <a name="configure-dns-in-the-on-premises-domain"></a>Konfigurera DNS i den lokala domänen

Om du vill lösa den hanterade Azure AD DS-domänen korrekt från den lokala miljön kan du behöva lägga till vidarebefordrare till de befintliga DNS-servrarna. Om du inte har konfigurerat den lokala miljön för att kommunicera med den Hanterade Azure AD DS-domänen slutför du följande steg från en hanteringsarbetsstation för den lokala AD DS-domänen:

1. Välj **Start | Administrativa verktyg | DNS**
1. Högerklicka-DNS-server, till exempel *myAD01*, välj **Egenskaper**
1. Välj **Vidarebefordrare**och **sedan Redigera** om du vill lägga till ytterligare vidarebefordrare.
1. Lägg till IP-adresserna för den hanterade Azure AD DS-domänen, till exempel *10.0.1.4* och *10.0.1.5*.

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>Skapa inkommande skogsförtroende i den lokala domänen

Den lokala AD DS-domänen behöver ett inkommande skogsförtroende för den Azure AD DS-hanterade domänen. Det här förtroendet måste skapas manuellt i den lokala AD DS-domänen, det kan inte skapas från Azure-portalen.

Om du vill konfigurera inkommande förtroende för den lokala AD DS-domänen slutför du följande steg från en hanteringsarbetsstation för den lokala AD DS-domänen:

1. Välj **Start | Administrativa verktyg | Active Directory domäner och förtroenden**
1. Högervalsdomän, till exempel *onprem.contoso.com*, välj **Egenskaper**
1. Välj fliken **Förtroenden** och sedan **Nytt förtroende**

   > [!NOTE]
   > Om du inte ser menyalternativet **Förtroenden** kontrollerar du under **Egenskaper** för *skogstypen*. Endast *resursskogar* kan skapa förtroenden. Om skogstypen är *Användare*kan du inte skapa förtroenden. Det finns för närvarande inget sätt att ändra skogstypen för en Azure AD DS-hanterad domän. Du måste ta bort och återskapa den hanterade domänen som en resursskog.

1. Ange namn på Azure AD DS-domännamn, till exempel *aaddscontoso.com*och välj sedan **Nästa**
1. Välj alternativet för att skapa ett **Skogsförtroende**och skapa sedan ett **enkelriktade sätt: inkommande** förtroende.
1. Välj att skapa förtroendet för den **här domänen .** I nästa steg skapar du förtroendet för Azure-portalen för azure AD DS-hanterad domän.
1. Välj att använda **Skogsomfattande autentisering**och ange och bekräfta ett förtroendelösenord. Samma lösenord anges också i Azure-portalen i nästa avsnitt.
1. Gå igenom de närmaste fönstren med standardalternativ och välj sedan alternativet för **Nej, bekräfta inte det utgående förtroendet**.
1. Välj **Slutför**

## <a name="create-outbound-forest-trust-in-azure-ad-ds"></a>Skapa utgående skogsförtroende i Azure AD DS

Med den lokala AD DS-domänen konfigurerad för att lösa den Azure AD DS-hanterade domänen och ett inkommande skogsförtroende som skapats har det utgående skogsförtroendet nu skapat det utgående skogsförtroendet. Det här utgående skogsförtroendet slutför förtroenderelationen mellan den lokala AD DS-domänen och azure AD DS-hanterad domän.

Så här skapar du det utgående förtroendet för den Azure AD DS-hanterade domänen i Azure-portalen:

1. Sök efter och välj **Azure AD Domain Services**i Azure-portalen och välj sedan din hanterade domän, till exempel *aaddscontoso.com*
1. Välj **Förtroenden**på menyn till vänster i azure AD DS-hanterade domänen och välj sedan till **+ Lägg till** ett förtroende.
1. Ange ett visningsnamn som identifierar ditt förtroende och sedan det lokala betrodda skogs-DNS-namnet, till exempel *onprem.contoso.com*
1. Ange samma förtroendelösenord som användes när du konfigurerade det inkommande skogsförtroendet för den lokala AD DS-domänen i föregående avsnitt.
1. Tillhandahålla minst två DNS-servrar för den lokala AD DS-domänen, till exempel *10.0.2.4* och *10.0.2.5*
1. Spara det utgående skogsförtroendet **när** du är klar

    [Skapa förtroende för utgående skog i Azure-portalen](./media/create-forest-trust/portal-create-outbound-trust.png)

## <a name="validate-resource-authentication"></a>Verifiera resursautentisering

I följande vanliga scenarier kan du verifiera att skogsförtroendet autentiserar användare och åtkomst till resurser på rätt sätt:

* [Lokal användarautentisering från Azure AD DS-resursskogen](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [Komma åt resurser i Azure AD DS-resursskogen med lokala användare](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [Aktivera fil- och skrivardelning](#enable-file-and-printer-sharing)
    * [Skapa en säkerhetsgrupp och lägga till medlemmar](#create-a-security-group-and-add-members)
    * [Skapa en filresurs för åtkomst över skogen](#create-a-file-share-for-cross-forest-access)
    * [Verifiera autentisering över flera skogar till en resurs](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Lokal användarautentisering från Azure AD DS-resursskogen

Du bör ha den virtuella windows server-datorn ansluten till Azure AD DS-resursdomänen. Använd den här virtuella datorn för att testa din lokala användare kan autentisera på en virtuell dator.

1. Anslut till den virtuella datorn för Windows Server som är ansluten till Azure AD DS-resursskogen med fjärrskrivbord och dina Azure AD DS-administratörsbehörighet. Om du får ett NLA-fel (Network Level Authentication) kontrollerar du att användarkontot du använde inte är ett domänanvändarkonto.

    > [!NOTE]
    > Om du vill ansluta till dina virtuella datorer som är anslutna till Azure AD Domain Services på ett säkert sätt kan du använda [Azure Bastion Host Service](https://docs.microsoft.com/azure/bastion/bastion-overview) i Azure-regioner som stöds.

1. Öppna en kommandotolk `whoami` och använd kommandot för att visa det unika namnet på den autentiserade användaren:

    ```console
    whoami /fqdn
    ```

1. Använd `runas` kommandot för att autentisera som användare från den lokala domänen. I följande kommando `userUpn@trusteddomain.com` ersätter du UPN för en användare från den betrodda lokala domänen. Kommandot frågar dig för användarens lösenord:

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. Om autentiseringen lyckas öppnas en ny kommandotolk. Rubriken på den nya `running as userUpn@trusteddomain.com`kommandotolken innehåller .
1. Använd `whoami /fqdn` i den nya kommandotolken för att visa det unika namnet på den autentiserade användaren från den lokala Active Directory.

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>Komma åt resurser i Azure AD DS-resursskogen med lokala användare

Med hjälp av den virtuella datorn för Windows Server som är ansluten till Azure AD DS-resursskogen kan du testa scenariot där användare kan komma åt resurser som finns i resursskogen när de autentiserar från datorer i den lokala domänen med användare från den lokala domänen. Följande exempel visar hur du skapar och testar olika vanliga scenarier.

#### <a name="enable-file-and-printer-sharing"></a>Aktivera fil- och skrivardelning

1. Anslut till den virtuella datorn för Windows Server som är ansluten till Azure AD DS-resursskogen med fjärrskrivbord och dina Azure AD DS-administratörsbehörighet. Om du får ett NLA-fel (Network Level Authentication) kontrollerar du att användarkontot du använde inte är ett domänanvändarkonto.

    > [!NOTE]
    > Om du vill ansluta till dina virtuella datorer som är anslutna till Azure AD Domain Services på ett säkert sätt kan du använda [Azure Bastion Host Service](https://docs.microsoft.com/azure/bastion/bastion-overview) i Azure-regioner som stöds.

1. Öppna **Windows-inställningar**och sök sedan efter och välj **Nätverks- och delningscenter**.
1. Välj alternativet ändra **avancerade delningsinställningar.**
1. Under **domänprofilen**väljer du **Aktivera fil- och skrivardelning** och sparar sedan **ändringar**.
1. Stäng **nätverks- och delningscenter**.

#### <a name="create-a-security-group-and-add-members"></a>Skapa en säkerhetsgrupp och lägga till medlemmar

1. Öppna **Active Directory-användare och datorer**.
1. Högerklicka på domännamnet, välj **Nytt**och välj sedan **Organisationsenhet**.
1. Skriv *LocalObjects*i namnrutan och välj sedan **OK**.
1. Markera och högerklicka på **LocalObjects** i navigeringsfönstret. Välj **Nytt** och **gruppera**sedan .
1. Skriv *FileServerAccess* i rutan **Gruppnamn.** För **gruppomfattningen**väljer du **Domänlokal**och väljer sedan **OK**.
1. Dubbelklicka på **FileServerAccess**i innehållsfönstret . Välj **Medlemmar**, välj **Lägg till**och välj sedan **Platser**.
1. Välj din lokala Active Directory i **platsvyn** och välj sedan **OK**.
1. Skriv *Domänanvändare* i rutan **Ange de objektnamn som ska markeras.** Välj **Kontrollera namn**, ange autentiseringsuppgifter för den lokala Active Directory och välj sedan **OK**.

    > [!NOTE]
    > Du måste ange autentiseringsuppgifter eftersom förtroenderelationen bara är ett sätt. Det innebär att användare från Azure AD DS inte kan komma åt resurser eller söka efter användare eller grupper i den betrodda (lokala) domänen.

1. Gruppen **Domänanvändare** från den lokala Active Directory-filen ska vara medlem i gruppen **FileServerAccess.** Välj **OK** om du vill spara gruppen och stänga fönstret.

#### <a name="create-a-file-share-for-cross-forest-access"></a>Skapa en filresurs för åtkomst över skogen

1. Skapa en mapp och ange namn som *CrossForestShare*på den virtuella datorn för Windows Server som är ansluten till Azure AD DS-resursskogen.
1. Högerklicka på mappen och välj **Egenskaper**.
1. Välj fliken **Säkerhet** och välj sedan **Redigera**.
1. I dialogrutan *Behörigheter för CrossForestShare* väljer du **Lägg till**.
1. Skriv *FileServerAccess* i **Ange de objektnamn som ska markeras**och välj sedan **OK**.
1. Välj *FileServerAccess* i listan **Grupper eller användarnamn.** I listan **Behörigheter för FileServerAccess** väljer du *Tillåt* för behörigheterna **Ändra** och **Skriv** och väljer sedan **OK**.
1. Välj fliken **Delning** och välj sedan **Avancerad delning...**
1. Välj **Dela den här mappen**och ange sedan ett minnesvärt namn för filresursen i Dela **namn** som *CrossForestShare*.
1. Välj **Behörigheter**. Välj **Tillåt** **behörighet** i listan **Behörigheter för alla.**
1. Välj **OK** två gånger och **stäng**sedan .

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>Verifiera autentisering över flera skogar till en resurs

1. Logga in på en Windows-dator som är ansluten till din lokala Active Directory med ett användarkonto från den lokala Active Directory.
1. Med **Utforskaren**ansluter du till resursen som du skapade med `\\fs1.aaddscontoso.com\CrossforestShare`det fullständigt kvalificerade värdnamnet och resursen, till exempel .
1. Om du vill verifiera skrivbehörigheten högerklickar du i mappen, väljer **Nytt**och väljer sedan **Textdokument**. Använd standardnamnet **Nytt textdokument**.

    Om skrivbehörigheterna är korrekt inställda skapas ett nytt textdokument. Följande steg öppnas, redigeras och tas bort filen efter behov.
1. Om du vill verifiera läsbehörigheten öppnar du **Nytt textdokument**.
1. Om du vill verifiera ändringsbehörigheten lägger du till text i filen och stänger **Anteckningar**. När du uppmanas att spara ändringar väljer du **Spara**.
1. Om du vill verifiera borttagningsbehörigheten högerklickar du **på Nytt textdokument** och väljer **Ta bort**. Välj **Ja** för att bekräfta borttagning av filer.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera DNS i en lokal AD DS-miljö för att stödja Azure AD DS-anslutning
> * Skapa ett enkelriktad inkommande skogsförtroende i en lokal AD DS-miljö
> * Skapa ett envägs utgående skogsförtroende i Azure AD DS
> * Testa och verifiera förtroenderelationen för autentisering och resursåtkomst

Mer begreppsmässig information om skogstyper i Azure AD DS finns i [Vad är resursskogar?][concepts-forest] [How do forest trusts work in Azure AD DS?][concepts-trust]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[howto-change-sku]: change-sku.md
