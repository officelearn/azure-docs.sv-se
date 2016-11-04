---
title: Katalogintegrering mellan Azure Multi-Factor Authentication och Active Directory
description: Det här är sidan om Azure Multi-Factor Authentication som beskriver hur du integrerar Azure Multi-Factor Authentication Server med Active Directory så att du kan synkronisera katalogerna.
services: multi-factor-authentication
documentationcenter: ''
author: kgremban
manager: femila
editor: curtand

ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/04/2016
ms.author: kgremban

---
# Katalogintegrering mellan Azure MFA Server och Active Directory
I avsnittet Katalogintegrering kan du konfigurera servern för integrering med Active Directory eller en annan LDAP-katalog.  På så sätt kan du konfigurera attribut för att matcha katalogschemat och ställa in automatisk synkronisering av användare.

## Inställningar
Som standard konfigureras Azure Multi-Factor Authentication Server att importera eller synkronisera användare från Active Directory.  På fliken kan du åsidosätta standardbeteendet och binda till en annan LDAP-katalog, en ADAM-katalog eller en särskild Active Directory-domänkontrollant.  Fliken innehåller också inställningar för användning av LDAP-autentisering via proxy eller för LDAP-bindning som ett RADIUS-mål, förautentisering för IIS-autentisering eller primär autentisering för användarportalen.  De enskilda inställningarna beskrivs i följande tabell.

![Inställningar](./media/multi-factor-authentication-get-started-server-dirint/dirint.png)

| Funktion | Beskrivning |
| --- | --- |
| Använd Active Directory |Välj alternativet Använd Active Directory om du vill använda Active Directory för import och synkronisering.  Det här är standardinställningen. <br>Obs! Datorn måste vara ansluten till en domän och du måste vara inloggad med ett domänkonto för att Active Directory-integreringen ska fungera korrekt. |
| Inkludera betrodda domäner |Markera kryssrutan Inkludera betrodda domäner om du vill att agenten ska försöka ansluta till domäner som är betrodda av den aktuella domänen, en annan domän i skogen eller domäner som ingår i ett skogsförtroende.  Om du inte ska importera eller synkronisera användare från någon av de betrodda domänerna avmarkerar du kryssrutan för att förbättra prestanda.  Kryssrutan är markerad som standard. |
| Använd specifik LDAP-konfiguration |Välj alternativet Använd LDAP om du vill använda LDAP-inställningarna som angetts för import och synkronisering. Obs! Om du väljer Använd LDAP ändras referenserna i användargränssnittet från Active Directory till LDAP. |
| Knappen Redigera |Du kan använda knappen Redigera om du vill redigera de aktuella LDAP-inställningarna. |
| Använd attributbegränsade frågor |Anger om attributbegränsade frågor ska användas.  Med attributbegränsade frågor kan du göra effektiva katalogsökningar efter kvalificerade poster baserat på posterna i en annan posts attribut.  Azure Multi-Factor Authentication Server använder attributbegränsade frågor för att effektivt fråga användare som är medlemmar i en säkerhetsgrupp.   <br>Obs! Det finns vissa fall där attributbegränsade frågor stöds, men inte bör användas.  Active Directory kan exempelvis ha problem med attributbegränsade frågor när en säkerhetsgrupp innehåller medlemmar från mer än en domän.  I detta fall bör kryssrutan avmarkeras. |

LDAP-inställningarna beskrivs i följande tabell.

| Funktion | Beskrivning |
| --- | --- |
| Server |Ange värdnamnet eller IP-adressen för servern som kör LDAP-katalogen.  En sekundär server kan också anges, avgränsad med semikolon. <br>Obs! När bindningstypen är SSL krävs normalt ett fullständigt kvalificerat värdnamn. |
| Grundläggande unikt namn |Ange det unika namnet på baskatalogobjektet som alla katalogfrågor ska starta från.  Till exempel dc=abc,dc=com. |
| Bindningstyp – frågor |Välj lämplig bindningstyp som ska användas vid bindning för att söka i LDAP-katalogen.  Detta används för importer, synkronisering och matchning av användarnamn. <br><br>  Anonym – en anonym bindning utförs.  Unikt namn för bindning och Bindningslösenord används inte.  Detta fungerar bara om LDAP-katalogen tillåter anonym bindning och behörigheterna tillåter frågor efter relevanta poster och attribut.  <br><br> Enkelt – Unikt namn för bindning och Bindningslösenord skickas som klartext för att binda till LDAP-katalogen.  Detta bör endast användas i testsyfte för att kontrollera att servern kan nås och att bindningskontot har lämplig åtkomst.  Vi rekommenderar att du använder SSL i stället när rätt certifikat har installerats.  <br><br> SSL – Unikt namn för bindning och Bindningslösenord krypteras med SSL för att binda till LDAP-katalogen.  Detta kräver att ett certifikat installeras lokalt som LDAP-katalogen litar på.  <br><br> Windows – Bind användarnamn och Bindningslösenord används för att på ett säkert sätt ansluta till en Active Directory-domänkontrollant eller en ADAM-katalog.  Om Bind användarnamn lämnas tomt används det inloggade användarkontot för att binda. |
| Bindningstyp – autentiseringar |Välj lämplig bindningstyp som ska användas när LDAP-bindningsautentisering utförs.  Se beskrivningarna av bindningstyperna under Bindningstyp – frågor.  Detta gör till exempel att en anonym bindning kan användas för frågor medan SSL-bindning används för att skydda LDAP-bindningsautentiseringar. |
| Unikt namn för bindning eller Bind användarnamn |Ange det unika namnet för användarposten för kontot som ska användas vid bindningar till LDAP-katalogen.<br><br>Det unika namnet för bindning används endast när bindningstypen är Enkel eller SSL.  <br><br>Ange användarnamnet för Windows-kontot som ska användas vid bindningar till LDAP-katalogen när bindningstypen är Windows.  Om inget anges används den inloggade användarens konto för att binda. |
| Bindningslösenord |Ange bindningslösenordet för det unika namnet för bindning eller användarnamnet som används för att binda till LDAP-katalogen.  Om du vill konfigurera lösenordet för tjänsten Multi-Factor Auth Server AdSync måste synkronisering vara aktiverat och tjänsten måste köras på den lokala datorn.  Lösenordet sparas i Lagrade användarnamn och lösenord i Windows under det konto som tjänsten Multi-Factor Auth Server AdSync körs som.  Lösenordet sparas även under kontot som användargränssnittet för Multi-Factor Auth Server körs som och under kontot som tjänsten Multi-Factor Auth Server körs som.  <br><br> Obs! Eftersom lösenordet bara lagras i Lagrade användarnamn och lösenord i Windows på den lokala servern måste detta steg utföras på varje Multi-Factor Auth-server som behöver åtkomst till lösenordet. |
| Storleksgränsen för fråga |Ange storleksgränsen för det högsta antalet användare som en katalogsökning ska returnera.  Den här gränsen måste matcha LDAP-katalogens konfiguration.  För stora sökningar som inte stöder sidindelning försöker importerna och synkroniseringarna att hämta användare batchvis.  Om storleksgränsen som anges här är större än den gräns som konfigurerats för LDAP-katalogen kanske vissa användare missas. |
| Knappen Testa |Klicka på knappen Testa om du vill testa bindningen till LDAP-servern.  <br><br> Obs! Du måste inte välja alternativet Använd LDAP för att testa en bindning.  På så sätt kan bindningen testas innan LDAP-konfigurationen används. |

## Filter
Med filter kan du ange villkor för att kvalificera poster när du utför en katalogsökning.  Genom att ange filtret kan du definiera de objekt som du vill synkronisera.  

![Filter](./media/multi-factor-authentication-get-started-server-dirint/dirint2.png)

Azure Multi-Factor Authentication har följande tre alternativ.

* **Behållarfilter** – Ange filtervillkoren som används för att kvalificera behållarposter när en katalogsökning utförs.  För Active Directory och ADAM används oftast (|(objectClass=organizationalUnit)(objectClass=container)).  För andra LDAP-kataloger bör filtervillkoren som kvalificerar varje typ av behållarobjekt användas beroende på katalogschemat.  <br>Obs! Om det lämnas tomt används ((objectClass=organizationalUnit)(objectClass=container)) som standard.
* **Säkerhetsgruppfilter** – Ange filtervillkoren som används för att kvalificera säkerhetsposter när en katalogsökning utförs.  För Active Directory och ADAM används oftast (&(objectCategory=group)(groupType:1.2.840.113556.1.4.804:=-2147483648)).  För andra LDAP-kataloger bör filtervillkoren som kvalificerar varje typ av säkerhetsgruppsobjekt användas beroende på katalogschemat.  <br>Obs! Om det lämnas tomt används (&(objectCategory=group)(groupType:1.2.840.113556.1.4.804:=-2147483648)) som standard.
* **Användarfilter** – Ange filtervillkoren som används för att kvalificera användarposter när en katalogsökning utförs.  För Active Directory och ADAM används oftast (&(objectClass=user)(objectCategory=person)).  För andra LDAP-kataloger bör (objectClass=inetOrgPerson) eller något liknande användas beroende på katalogschemat. <br>Obs! Om det lämnas tomt används (&(objectCategory=person)(objectClass=user)) som standard.

## Attribut
Attributen kan anpassas efter behov för en viss katalog.  På så sätt kan du lägga till anpassade attribut och finjustera synkroniseringen till endast de attribut som du behöver.  Värdet för varje attributfält bör vara namnet på attributet så som det definieras i katalogschemat.  Mer information finns i tabellen nedan.

![Attribut](./media/multi-factor-authentication-get-started-server-dirint/dirint3.png)

| Funktion | Beskrivning |
| --- | --- |
| Unik identifierare |Ange attributnamnet för attributet som fungerar som den unika identifieraren för behållar-, säkerhetsgrupps- och användarposter.  I Active Directory är detta vanligtvis objectGUID.  I andra LDAP-implementeringar kan det vara entryUUID eller något liknande.  Standardvärdet är objectGUID. |
| ... ”Välj attribut”-knappar |Bredvid varje attributfält finns en ”...”-knapp som visar dialogrutan Välj attribut där du kan välja ett attribut från en lista. <br><br>Dialogrutan Välj attribut.<br><br>Obs! Attribut kan anges manuellt och behöver inte matcha ett attribut i attributlistan. |
| Typ av unik identifierare |Välj typen för attributet för unik identifierare.  I Active Directory har objectGUID-attributet typen GUID.  I andra LDAP-implementeringar kan typen vara ASCII, byte-matris eller Sträng.  Standardvärdet är GUID. <br><br>Obs! Det är viktigt att detta anges korrekt eftersom synkroniseringsobjektens unika identifierare används för att referera till objekten och eftersom typen av unik identifierare används för att hitta objekten i katalogen.  Om du anger detta till Sträng trots att katalogen lagrar värdet som en byte-matris med ASCII-tecken så fungerar inte synkroniseringen korrekt. |
| Unikt namn |Ange attributnamnet för attributet som innehåller det unika namnet för varje post.  I Active Directory är detta normalt distinguishedName.  I andra LDAP-implementeringar kan det vara entryDN eller något liknande.  Standardvärdet är distinguishedName. <br><br>Obs! Om det inte finns något attribut som bara innehåller det unika namnet kan adspath användas.  ”LDAP://<server>/”-delen av sökvägen rensas automatiskt så att bara objektets unika namn är kvar. |
| Behållarens namn |Ange attributnamnet för attributet som innehåller namnet i en behållarpost.  Värdet för det här attributet visas i behållarhierarkin när du importerar från Active Directory eller lägger till synkroniseringsobjekt.  Standardvärdet är name. <br><br>Obs! Om olika behållare använder olika attribut för sina namn kan flera attribut för behållarnamn anges avgränsade med semikolon.  Det första attributet för behållarnamn som hittas i ett behållarobjekt används för att visa dess namn. |
| Namn på säkerhetsgrupp |Ange attributnamnet för attributet som innehåller namnet i en säkerhetsgruppspost.  Värdet för det här attributet visas i säkerhetsgruppslistan när du importerar från Active Directory eller lägger till synkroniseringsobjekt.  Standardvärdet är name. |
| Användare |Följande attribut används för att söka efter, visa, importera och synkronisera användarinformation från katalogen. |
| Användarnamn |Ange attributnamnet för attributet som innehåller användarnamnet i en användarpost.  Värdet för det här attributet används som Multi-Factor Auth Server-användarnamnet.  Ett andra attribut kan anges som en reserv till det första.  Det andra attributet används endast om det första attributet inte innehåller ett värde för användaren.  Standardvärdena är userPrincipalName och sAMAccountName. |
| Förnamn |Ange attributnamnet för attributet som innehåller förnamnet i en användarpost.  Standardvärdet är givenName. |
| Efternamn |Ange attributnamnet för attributet som innehåller efternamnet i en användarpost.  Standardvärdet är sn. |
| E-postadress |Ange attributnamnet för attributet som innehåller e-postadressen i en användarpost.  E-postadressen används för att skicka välkomst- och uppdateringsmeddelanden till användaren via e-post.  Standardvärdet är mail. |
| Användargrupp |Ange attributnamnet för attributet som innehåller användargruppen i en användarpost.  Användargrupp kan användas för att filtrera användare i agenten och rapporter på Multi-Factor Auth Server-hanteringsportalen. |
| Beskrivning |Ange attributnamnet för attributet som innehåller beskrivningen i en användarpost.  Beskrivningen används endast för sökning.  Standardvärdet är description. |
| Språk för röstsamtal |Ange attributnamnet för attributet som innehåller kortnamnet för språket som ska användas för röstsamtal för användaren. |
| Språk för SMS-meddelanden |Ange attributnamnet för attributet som innehåller kortnamnet för språket som ska användas för SMS-meddelanden för användaren. |
| Språk för telefonapp |Ange attributnamnet för attributet som innehåller kortnamnet för språket som ska användas för textmeddelanden i telefonappar för användaren. |
| OATH-tokenspråk |Ange attributnamnet för attributet som innehåller kortnamnet för språket som ska användas för OATH-tokenmeddelanden för användaren. |
| Telefoner |Följande attribut används för att importera eller synkronisera telefonnummer för användare.  Om inget attributnamn anges för telefontypen är telefontypen inte tillgänglig när du importerar från Active Directory eller lägger till synkroniseringsobjekt. |
| Företag |Ange attributnamnet för attributet som innehåller företagstelefonnumret i en användarpost.  Standardvärdet är telephoneNumber. |
| Hem |Ange attributnamnet för attributet som innehåller hemtelefonnumret i en användarpost.  Standardvärdet är homePhone. |
| Personsökare |Ange attributnamnet för attributet som innehåller personsökarnumret i en användarpost.  Standardvärdet är pager. |
| Mobil |Ange attributnamnet för attributet som innehåller mobiltelefonnumret i en användarpost.  Standardvärdet är mobile. |
| Fax |Ange attributnamnet för attributet som innehåller faxnumret i en användarpost.  Standardvärdet är facsimileTelephoneNumber. |
| IP-telefon |Ange attributnamnet för attributet som innehåller IP-telefonnumret i en användarpost.  Standardvärdet är ipPhone. |
| Anpassat |Ange attributnamnet för attributet som innehåller ett anpassat telefonnummer i |
| en användarpost.  Standardvärdet är tomt. | |
| Anknytning |Ange attributnamnet för attributet som innehåller anknytningsnumret i en användarpost.  Värdet för anknytningsfältet används endast som anknytningen till det primära telefonnumret.  Standardvärdet är tomt. <br><br>Obs! Om attributet Anknytning inte anges kan anknytningar ingå som en del av telefonattributet.  Anknytningen måste föregås med ett ”x” så att den kan parsas.  I exempelnumret 555-123-4567 x890 tolkas 555-123-4567 som telefonnumret och 890 som anknytningen. |
| Knappen Återställ standardvärden |Klicka på knappen Återställ standardvärden om du vill återställa alla attribut till deras standardvärden.  Standardvärdena bör fungera korrekt med det vanliga Active Directory- eller ADAM-schemat. |

Om du vill redigera attribut klickar du på redigeringsknappen på fliken Attribut.  När du gör det visas ett fönster där du kan redigera attributen.

![Redigera attribut](./media/multi-factor-authentication-get-started-server-dirint/dirint4.png)

## Synkronisering
Synkroniseringen ser till att Azure Multi-Factor databasen är synkroniserad med användarna i Active Directory eller en annan LDAP-katalog (Lightweight Directory Access Protocol.  Processen påminner om när du importerar användare manuellt från Active Directory, men söker regelbundet efter ändringar i Active Directory-användare och säkerhetsgrupper som behöver bearbetas.  Den ger också möjlighet att inaktivera eller ta bort användare som har tagits bort från en behållare eller säkerhetsgrupp och att ta bort användare som har tagits bort från Active Directory.

Tjänsten Multi-Factor Auth ADSync är en Windows-tjänst som utför den periodiska avsökningen av Active Directory.  Detta ska inte förväxlas med Azure AD Sync eller Azure AD Connect.  Multi-Factor Auth ADSync bygger visserligen på en liknande kodbas, men är specifik för Azure Multi-Factor Authentication-servern.  Den installeras i stoppat läge och startas av Multi-Factor Auth  Server-tjänsten när den konfigurerats att köra.  Om du har en Multi-Factor Auth Server-konfiguration med flera servrar kan Multi-Factor Auth ADSync endast köras på en enskild server.

Tjänsten Multi-Factor Auth ADSync använder DirSync LDAP-servertillägget från Microsoft för att effektivt söka efter ändringar.  Den här DirSync-kontrollanroparen måste ha rättigheten ”Katalogtjänständringar” och den utökade åtkomsträttigheten DS-Replication-Get-Changes.  Som standard tilldelas dessa rättigheter till kontona Administratör och LocalSystem på domänkontrollanter.  Tjänsten Multi-Factor Auth AdSync är konfigurerad att köras som LocalSystem som standard.  Därför är det enklast att köra tjänsten på en domänkontrollant.  Tjänsten kan köras som ett konto med lägre behörighet om du konfigurerar den att alltid utföra en fullständig synkronisering.  Detta är mindre effektivt, men kräver mindre kontoprivilegier.

Om tjänsten konfigureras att använda LDAP och LDAP-katalogen stöder DirSync-kontrollen så fungerar sökningen efter användar- och säkerhetsgruppsändringar på samma sätt som den gör med Active Directory.  Om LDAP-katalogen inte stöder DirSync-kontrollen utförs en fullständig synkronisering under varje cykel.

![Synkronisering](./media/multi-factor-authentication-get-started-server-dirint/dirint5.png)

Tabellen nedan innehåller mer information om de enskilda inställningarna på fliken Synkronisering.

| Funktion | Beskrivning |
| --- | --- |
| Aktivera synkronisering med Active Directory |Om inställningen är aktiverad startas Multi-Factor Auth Server-tjänsten regelbundet för att söka efter ändringar i Active Directory. <br><br>Obs! Minst ett synkroniseringsobjekt måste läggas till och Synkronisera nu måste köras innan Multi-Factor Auth Server- tjänsten börjar bearbeta ändringar. |
| Synkronisera var |Ange tidsintervallet som Multi-Factor Auth Server-tjänsten ska vänta mellan att göra avsökningar och bearbeta ändringar. <br><br> Obs! Det angivna intervallet är tiden mellan början av varje cykel.  Om tiden för att bearbeta ändringar överskrider intervallet startar tjänsten en ny avsökning direkt. |
| Ta bort användare som inte längre finns i Active Directory |Om du väljer det här alternativet bearbetar tjänsten Multi-Factor Auth Server tombstone-objekten för borttagna användare i Active Directory och tar bort relaterade Multi-Factor Auth Server-användare. |
| Utför alltid en fullständig synkronisering |Om du väljer det här alternativet utför tjänsten Multi-Factor Auth Server alltid en fullständig synkronisering.  Om alternativet är avmarkerat utför tjänsten Multi-Factor Auth Server en inkrementell synkronisering genom att endast fråga efter användare som har ändrats.  Alternativet är avmarkerat som standard. <br><br> Obs! Om alternativet är avmarkerat kan en inkrementell synkronisering endast utföras om katalogen stöder DirSync-kontroll och kontot som används för att binda till katalogen har rätt behörighet för att utföra inkrementella DirSync-frågor.  Om kontot inte har rätt behörighet eller om flera domäner är inblandade i synkroniseringen rekommenderar vi att du utför en fullständig synkronisering. |
| Kräv administratörsgodkännande om fler än X användare ska inaktiveras eller tas bort |Synkroniseringsobjekt kan konfigureras att inaktivera eller ta bort användare som inte längre är medlemmar i objektets behållare eller säkerhetsgrupp.  Som ett skydd kan godkännande från en administratör krävas om antalet användare som ska inaktiveras eller tas bort överskrider ett tröskelvärde.  Om det här alternativet är markerat krävs godkännande för det angivna tröskelvärdet.  Standardvärdet är 5 och intervallet är 1 till 999. <br><br> Godkännandet börjar med att ett e-postmeddelande skickas till administratören. E-postmeddelandet innehåller anvisningar för att granska och godkänna inaktiveringen och borttagningen av användare.  När användargränssnittet för Multi-Factor Auth Server startas uppmanas administratören att ge sitt godkännande. |

Med knappen **Synkronisera nu** kan du köra en fullständig synkronisering för de angivna synkroniseringsobjekten.  En fullständig synkronisering krävs om du lägger till, ändrar, tar bort eller ändrar ordning på synkroniseringsobjekt.  Det krävs också för att starta tjänsten Multi-Factor Auth AdSync eftersom det anger startpunkten då tjänsten ska söka efter inkrementella ändringar.  Om synkroniseringsobjekt har ändrats och ingen fullständig synkronisering har utförts uppmanas du att välja Synkronisera nu när du navigerar till ett annat avsnitt eller när du stänger användargränssnittet.

Med knappen **Ta bort** kan administratören ta bort ett eller flera synkroniseringsobjekt från listan med synkroniseringsobjekt i Multi-Factor Auth Server.

> [!WARNING]
> När posten för ett synkroniseringsobjekt har tagits bort kan det inte återställas. Du måste lägga till posten för synkroniseringsobjektet igen om du tar bort det av misstag.
> 
> 

Synkroniseringsobjektet eller synkroniseringsobjekten har tagits bort från Multi-Factor Auth Server.  Tjänsten Multi-Factor Auth Server kommer inte längre att bearbeta synkroniseringsobjekten.

Med knapparna Flytta upp och Flytta ned kan administratören ändra ordning på synkroniseringsobjekten.  Ordningen är viktig eftersom samma användare kan vara medlem i mer än ett synkroniseringsobjekt (t.ex. en behållare och en säkerhetsgrupp).  Inställningarna som tillämpas på användaren under synkroniseringen kommer från det första synkroniseringsobjektet i listan som användaren är associerad med.  Därför bör synkroniseringsobjekten placeras i prioritetsordning.

> [!TIP]
> En fullständig synkronisering bör utföras när du har tagit bort synkroniseringsobjekt.  En fullständig synkronisering bör utföras när du har ändrat ordning på synkroniseringsobjekt.  Klicka på Synkronisera nu om du vill utföra en fullständig synkronisering.
> 
> 

## Multi-Factor Auth-servrar
Ytterligare Multi-Factor Auth-servrar kan konfigureras och fungera som ett reservalternativ i form av en RADIUS-proxy, LDAP-proxy eller för IIS-autentisering. Synkroniseringskonfigurationen delas mellan alla agenter. Men Multi-Factor Auth Server-tjänsten kan bara köras på en av dessa agenter. På den här fliken kan du välja den Multi-Factor Auth-server som ska aktiveras för synkronisering.

![Multi-Factor Auth-servrar](./media/multi-factor-authentication-get-started-server-dirint/dirint6.png)

<!--HONumber=Sep16_HO3-->


