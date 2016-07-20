<properties
   pageTitle="Detaljerad genomgång av Azure Active Directory B2B-samarbete (förhandsversion) | Microsoft Azure"
   description="Azure Active Directory B2B-samarbete ger stöd för dina företagsomfattande relationer genom att tilldela affärspartner selektiv åtkomst till dina affärsprogram"
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# Azure AD B2B-samarbete (förhandsversion): Detaljerad genomgång

Den här genomgången beskriver hur du använder Azure AD B2B-samarbete. Som IT-administratör för Contoso vill vi dela program med anställda från tre partnerföretag. Inget av partnerföretagen behöver ha Azure AD.

- Alice från partnerorganisationen Enkel.
- Bob, från partnerorganisationen Medel, behöver åtkomst till en uppsättning appar.
- Carol, från partnerorganisationen Komplex, behöver åtkomst till en uppsättning appar samt medlemskap i grupper på Contoso.

När inbjudningarna har skickats ut till partneranvändarna kan vi konfigurera dem i Azure AD för att bevilja åtkomst till appar och medlemskap till grupper genom Azure-portalen. Vi ska börja med att lägga till Alice.

## Lägga till Alice i Contoso-katalogen
1. Skapa en CSV-fil med sidhuvudena som du ser här och fyll endast i **Email**, **DisplayName** och **InviteContactUsUrl** för Alice. **DisplayName** är det namn som visas i inbjudan och också det namn som visas i Contoso Azure AD-katalogen. **InviteContactUsUrl** är ett sätt för Alice att kontakta Contoso. I följande exempel anger InviteContactUsUrl LinkedIn-profilen för Contoso. Det är viktigt att du skriver etiketterna på den första raden i CSV-filen exakt som det anges i [referensen för CSV-filformatet](active-directory-b2b-references-csv-file-format.md).  
![CSV-exempelfil för Alice](./media/active-directory-b2b-detailed-walkthrough/AliceCSV.png)

2. Lägga till en användare i Contoso-katalogen på Azure-portalen (Active Directory > Contoso > Användare > Lägg till användare). Välj ”Användare i partnerföretag” i listrutan ”Typ av användare”. Ladda upp CSV-filen. Kontrollera att CSV-filen är stängd innan du laddar upp den.  
![CSV-filuppladdning för Alice](./media/active-directory-b2b-detailed-walkthrough/AliceUpload.png)

3. Alice visas nu som en extern användare i Contoso Azure AD-katalogen.  
![Alice visas i Azure AD](./media/active-directory-b2b-detailed-walkthrough/AliceInAD.png)

4. Alice får följande e-postmeddelande.  
![E-postinbjudan för Alice](./media/active-directory-b2b-detailed-walkthrough/AliceEmail.png)

5. Alice klickar på länken och uppmanas att tacka ja till inbjudan och att logga in med sina autentiseringsuppgifter för arbetet. Om Alice inte finns i Azure AD-katalogen uppmanas hon att registrera sig.  
![Registrering efter inbjudan för Alice](./media/active-directory-b2b-detailed-walkthrough/AliceSignUp.png)

6. Alice omdirigeras till appåtkomstpanelen som är tom tills hon beviljas åtkomst till appar.  
![Åtkomstpanel för Alice](./media/active-directory-b2b-detailed-walkthrough/AliceAccessPanel.png)

Den här proceduren ger stöd för den enklaste formen av B2B-samarbete. Som användare i Azure AD-katalogen för Contoso kan Alice beviljas åtkomst till program och grupper via Azure-portalen. Nu ska vi lägga till Bob, som behöver åtkomst till programmen Moodle och Salesforce.

## Lägga till Bob i Contoso-katalogen och bevilja åtkomst till appar
1. Använd Windows PowerShell med Azure AD-modulen installerad för att hitta Moodles och Salesforces program-ID:n. Du kan hämta ID:n med hjälp av cmdleten `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId` Nu visas en lista över alla tillgängliga program i Contoso och deras AppPrincialIds.  
![Hämta ID:n för Bob](./media/active-directory-b2b-detailed-walkthrough/BobPowerShell.png)

2. Skapa en CSV-fil som innehåller Bobs Email och DisplayName, **InviteAppID**, **InviteAppResources** och InviteContactUsUrl. Fyll i **InviteAppResources** med de AppPrincipalIds för Moodle och Salesforce som du fått via PowerShell, avgränsade med blanksteg. Fyll i **InviteAppId** med samma AppPrincipalId för Moodle för att lägga till en Moodle-logotyp i e-postmeddelandet och på inloggningssidan.  
![CSV-exempelfil för Bob](./media/active-directory-b2b-detailed-walkthrough/BobCSV.png)

3. Ladda upp CSV-filen via Azure Portal på samma sätt som du gjorde för Alice. Bob är nu en extern användare i Azure AD-katalogen för Contoso.

4. Bob får följande e-postmeddelande.  
![E-postinbjudan för Bob](./media/active-directory-b2b-detailed-walkthrough/BobEmail.png)

5. Bob klickar på länken och uppmanas att tacka ja till inbjudan. När han har loggat in dirigeras han till åtkomstpanelen och kan använda Moodle och Salesforce.  
![Åtkomstpanel för Bob](./media/active-directory-b2b-detailed-walkthrough/BobAccessPanel.png)

Nu ska vi lägga till Carol, som behöver åtkomst till program samt medlemskap i grupper i Contoso-katalogen.

## Lägga till Carol i Contoso-katalogen, beviljar åtkomst till appar och bevilja gruppmedlemskap

1. Använd Windows PowerShell med Azure AD-modulen installerad för att hitta program-ID:n och grupp-ID:n i Contoso.
 - Hämta AppPrincipalId med cmdleten `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId` (samma som för Bob)
 - Hämta ObjectId för grupper med hjälp av cmdleten `Get-MsolGroup | fl DisplayName, ObjectId`. Nu visas en lista över alla grupper i Contoso och deras ObjectIds. Grupp-ID:n kan också hämtas som objekt-ID:n på fliken Egenskaper för gruppen på Azure-portalen.  
![Hämta ID:n och grupper för Carol](./media/active-directory-b2b-detailed-walkthrough/CarolPowerShell.png)

2. Skapa CSV-filen och fyll i Carols Email, DisplayName, InviteAppID, InviteAppResources, **InviteGroupResources** och InviteContactUsUrl. **InviteGroupResources** fylls i baserat på ObjectId för grupperna MyGroup1 och Externals, avgränsade med blanksteg.  
![CSV-exempelfil för Carol](./media/active-directory-b2b-detailed-walkthrough/CarolCSV.png)

3. Ladda upp CSV-filen via Azure-portalen.

4. Carol är en användare i Contoso-katalogen och är även medlem i grupperna MyGroup1 och Externals, som du ser på Azure-portalen.  
![Carol visas i en grupp i Azure AD](./media/active-directory-b2b-detailed-walkthrough/CarolGroup.png)

5. Carol får ett e-postmeddelande som innehåller en länk för att tacka ja till inbjudan. När hon har loggat in omdirigeras hon till appåtkomstpanelen för att få åtkomst till Moodle och Salesforce.  

Detta är allt du behöver göra om du vill lägga till användare från partnerföretag i Azure AD B2B-samarbete. Den här genomgången demonstrerade hur du lägger till användarna Alice, Bob och Carol i Contoso-katalogen med hjälp av tre separata CSV-filer. Den här processen kan förenklas genom att de separata CSV-filerna slås ihop till en enda fil.  
![CSV-exempelfil för Alice, Bob och Carol](./media/active-directory-b2b-detailed-walkthrough/CombinedCSV.png)

## Relaterade artiklar
Läs andra artiklar om Azure AD B2B-samarbete:

- [Vad är Azure AD B2B-samarbete?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Hur det fungerar](active-directory-b2b-how-it-works.md)
- [Referens för CSV-filformatet](active-directory-b2b-references-csv-file-format.md)
- [Tokenformat för externa användare](active-directory-b2b-references-external-user-token-format.md)
- [Attributändringar i objekt för externa användare](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Aktuella begränsningar i förhandsversionen](active-directory-b2b-current-preview-limitations.md)
- [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md)



<!--HONumber=Jun16_HO2-->


