<properties
    pageTitle="Konfigurera Azure Active Directory för hantering av programåtkomst via självbetjäning| Microsoft Azure"
    description="Med grupphantering via självbetjäning kan användarna skapa och hantera säkerhetsgrupper eller Office 365-grupper i Azure Active Directory och begära medlemskap i säkerhetsgrupper eller Office 365-grupper"
    services="active-directory"
    documentationCenter=""
  authors="curtand"
    manager="stevenpo"
    editor=""
    />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/26/2016"
    ms.author="curtand"/>

# Konfigurera Azure Active Directory för grupphantering via självbetjäning

Med grupphantering via självbetjäning kan användare skapa och hantera säkerhetsgrupper eller Office 365-grupper i Azure Active Directory (AD Azure) och begära medlemskap i säkerhetsgrupper eller Office 365-grupper, som sedan kan godkännas eller avvisas av gruppens ägare. Med funktioner för grupphantering via självbetjäning kan den dagliga kontrollen av gruppmedlemskap delegeras till dem som förstår affärskontexten för medlemskapet. Funktioner för grupphantering via självbetjäning är bara tillgängliga för säkerhetsgrupper och Office 365-grupper, inte för e-postaktiverade säkerhetsgrupper eller distributionslistor.

Grupphantering via självbetjäning består för närvarande av två viktiga scenarier: delegerad grupphantering och grupphantering via självbetjäning.

- **Delegerad grupphantering** – Ett exempel är en administratör som hanterar åtkomsten till ett SaaS-program som hennes företag använder. Eftersom det börjar bli svårt att hantera dessa åtkomstbehörigheter ber administratören företagsägaren att skapa en ny grupp. Administratören tilldelar nu åtkomst till programmet till en ny grupp som företagets ägare precis har skapat och lägger till alla användare som för närvarande har åtkomst till programmet i den här gruppen. Företagsägaren kan sedan lägga till fler användare, som automatiskt tilldelas programmet en liten stund senare. Företagsägaren behöver inte vänta på att administratören ska utföra arbetet utan kan själva hantera åtkomsten för sina användare. Administratören kan göra samma sak för en administrativ assistent för en annan affärsgrupp, så kan både företagsägaren och assistenten hantera åtkomsten för sina användare – utan att kunna röra eller se varandras användare. Administratören kan fortfarande se alla användare som har åtkomst till programmet och blockera behörigheten om det behövs.

- **Grupphantering via självbetjäning** – Ett exempel på det här scenariot är två användare som båda har SharePoint Online-platser som de har konfigurerat oberoende av varandra, men som vill ge varandras team åtkomst till deras platser. För att åstadkomma detta kan de skapa en grupp i Azure AD, och sedan väljer båda två i SharePoint Online samma grupp för att ge åtkomst till varandras platser. När någon vill ha åtkomst begär de det från åtkomstpanelen och efter godkännande får de automatiskt åtkomst till båda SharePoint Online-platserna. Senare beslutar den ena av dem att alla användare som har åtkomst till hans plats även ska få åtkomst till ett visst SaaS-program. Han ber administratören för SaaS-programmet att lägga till åtkomstbehörigheter för programmet till hans plats. När det är gjort kommer alla begäranden som han godkänner att ge åtkomst till de två SharePoint Online-platserna och även till SaaS-programmet.

## Göra en grupp tillgänglig för självbetjäning av slutanvändare

Gå till fliken **Konfigurera** på [den klassiska Azure-portalen](https://manage.windowsazure.com), välj Aktiverat för **Delegerad grupphantering** och välj sedan Aktiverat för **Användare kan skapa säkerhetsgrupper** eller **Användare kan skapa Office-grupper**.

När **Användare kan skapa säkerhetsgrupper** är aktiverat kan alla användare i katalogen skapa nya säkerhetsgrupper och lägga till medlemmar i dessa grupper. Dessa nya grupper visas också på åtkomstpanelen för alla andra användare och andra användare kan skapa förfrågningar för att gå med i dessa grupper om principinställningen för gruppen tillåter det. Om **Användare kan skapa säkerhetsgrupper** är inaktiverat kan användare inte skapa grupper och kan inte ändra befintliga grupper som de äger, men de kan fortfarande hantera medlemskap i dessa grupper och godkänna förfrågningar från andra som vill bli medlemmar i deras grupper.

Du kan också använda **Användare som kan använda självbetjäning för säkerhetsgrupper** om du vill ha mer detaljerad åtkomstkontroll över funktionerna för grupphantering via självbetjäning för dina användare. När **Användare kan skapa grupper** är aktiverat kan alla användare i katalogen skapa nya grupper och lägga till medlemmar i dessa grupper. Genom att även ange **Användare som kan använda självbetjäning för säkerhetsgrupper** till Vissa begränsar du grupphanteringen till endast en begränsad grupp användare. När den här växeln har angetts till Vissa skapas en grupp med namnet SSGMSecurityGroupsUsers i din katalog och endast de användare som du har gjort till medlemmar i den här gruppen kan skapa nya säkerhetsgrupper och lägga till medlemmar i dessa grupper i din katalog. Om du väljer Alla för **Användare som kan använda självbetjäning för säkerhetsgrupper** kan alla användare i katalogen skapa nya grupper.

Du kan också använda rutan **Grupp som kan använda självbetjäning för säkerhetsgrupper** (som standard SSGMSecurityGroupsUsers) och ange ditt eget anpassade namn för en grupp som ska innehålla alla användare som kan använda självbetjäning och skapa nya grupper i katalogen.

## Ytterligare information

Dessa artiklar innehåller ytterligare information om Azure Active Directory.

* [Hantera åtkomst till resurser med Azure Active Directory-grupper](active-directory-manage-groups.md)

* [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md)

* [Vad är Azure Active Directory?](active-directory-whatis.md)

* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md)



<!--HONumber=Jun16_HO2-->


