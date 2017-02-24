Här följer begränsningarna för användning och andra tjänstebegränsningar för Azure Active Directory-tjänsten.

| Kategori | Begränsningar |
| --- | --- |
| Kataloger |En enskild användare kan bara associeras med högst 20 Azure Active Directory-kataloger.<br />Exempel på möjliga kombinationer: <ul> <li>En enskild användare skapar 20 kataloger.</li><li>En enskild användare läggs till i 20 kataloger som medlem.</li><li>En enskild användare skapar 10 kataloger som senare läggs till av andra i 10 olika kataloger.</li></ul> |
| Objekt |<ul><li>Högst 500 000 objekt kan användas i en katalog för användare med den kostnadsfria versionen av Azure Active Directory.</li><li>En icke-administratör kan skapa högst 250 objekt.</li></ul> |
| Schematillägg |<ul><li>Strängtypen tillägg kan innehålla maximalt 256 tecken. </li><li>Tillägg av typen binary är begränsade till 256 byte.</li><li>100 tilläggsvärden (över ALLA typer och ALLA program) kan skrivas till ett enskilt objekt.</li><li>Endast entiteter av typen ”User”, ”Group”, ”TenantDetail”, ”Device”, ”Application” och ”ServicePrincipal” kan utökas med typen ”String” eller enkelvärdesattribut av typen ”Binary”.</li><li>Schematillägg är bara tillgängliga i Graph API-version 1.21-preview. Programmet måste beviljas skrivbehörighet för att det ska vara möjligt att registrera ett tillägg.</li></ul> |
| Program |Högst 10 användare kan vara ägare till ett enda program. |
| Grupper |<ul><li>Högst 10 användare kan vara ägare till en enda grupp.</li><li>Valfritt antal objekt kan vara medlemmar i en grupp i Azure Active Directory.</li><li>Antalet medlemmar i en grupp som du kan synkronisera från din lokala Active Directory till Azure Active Directory är begränsat till 15 000 medlemmar, med hjälp av Azure Active Directory-katalogsynkronisering (DirSync).</li><li>Antalet medlemmar i en grupp som du kan synkronisera från din lokala Active Directory till Azure Active Directory med Azure AD Connect är begränsad till 50 000 medlemmar.</li></ul> |
| Åtkomstpanel |<ul><li>Det finns ingen gräns för antalet program som kan visas i åtkomstpanelen per slutanvändaren för användare som har tilldelats licenser för Azure AD Premium eller Enterprise Mobility Suite.</li><li>Högst 10 appaneler (exempel: Box, Salesforce eller Dropbox) visas på åtkomstpanelen för varje slutanvändare för användare som har tilldelats licenser kostnadsfritt eller Azure AD Basic-utgåvor av Azure Active Directory. Den här begränsningen gäller inte för administratörskonton.</li></ul> |
| Rapporter | Högst 1 000 rader kan visas eller hämtas i rapporter. Eventuella ytterligare data trunkeras. |
| Administrativa enheter | Ett objekt kan ingå i högst 30 administrativa enheter. |


<!--HONumber=Feb17_HO2-->


