Domain Name System (DNS) används för att hitta saker på internet. Till exempel när du anger en adress i webbläsaren eller på en länk på en webbsida, använder den DNS för att översätta domänen till en IP-adress. IP-adressen är till exempel en gatuadress, men det är inte mycket mänsklig eget. Exempelvis är det mycket enklare att komma ihåg en DNS-namn, till exempel **contoso.com** än att komma ihåg en IP-adress, till exempel 192.168.1.88 eller 2001:0:4137:1f67:24a2:3888:9cce:fea3.

DNS-systemet är baserad på *poster*. Poster associera en specifik *namn*, som **contoso.com**, med en IP-adress eller ett annat DNS-namn. När ett program, till exempel en webbläsare, leta upp ett namn i DNS, söker efter post och använder det pekar på som adressen. Om värdet för den pekar på är en IP-adress, använder webbläsaren värdet. Om den pekar på en annan DNS-namn har programmet göra upplösning igen. Slutligen går all namnmatchning ut om en IP-adress.

När du skapar en Azure-webbplats, tilldelas platsen automatiskt ett DNS-namn. Det här namnet i form av  **&lt;yoursitename&gt;. azurewebsites.net**. När du lägger till webbplatsen som en Azure Traffic Manager-slutpunkt webbplatsen sedan är tillgänglig via den  **&lt;yourtrafficmanagerprofile&gt;. trafficmanager.net** domän.

> [!NOTE]
> När webbplatsen har konfigurerats som en Traffic Manager-slutpunkt, använder du den **. trafficmanager.net** adressen när du skapar DNS-poster.
> 
> Du kan bara använda CNAME-poster med Traffic Manager
> 
> 

Det finns flera typer av poster med sina egna funktioner och begränsningar, men för webbplatser som är konfigurerade att som Traffic Manager-slutpunkter vi bara hand om en; *CNAME* poster.

### <a name="cname-or-alias-record"></a>Posten CNAME eller Alias
En CNAME-post mappar en *specifika* DNS-namn, exempelvis **mail.contoso.com** eller **www.contoso.com**, till en annan (kanoniska) domännamn. När det gäller Azure Websites med Traffic Manager kanoniska domännamnet är den  **&lt;myapp >. trafficmanager.net** domännamnet för Traffic Manager-profilen. När du skapat CNAME skapas ett alias för den  **&lt;myapp >. trafficmanager.net** domännamn. CNAME-posten ska matcha IP-adressen för din  **&lt;myapp >. trafficmanager.net** domännamn automatiskt, så om IP-adressen för webbplatsen ändras, inte behöver du vidta några åtgärder.

När trafik anländer på Traffic Manager, dirigerar sedan trafiken till din webbplats med hjälp av belastningsutjämningsmetod som den är konfigurerad för. Detta är helt transparent för besökare till din webbplats. De visas bara det anpassade domännamnet i webbläsaren.

> [!NOTE]
> Vissa domän-registratorer kan du mappa underdomäner när du använder en CNAME-post som bara **www.contoso.com**, och inte rot-namn som **contoso.com**. Mer information om CNAME-poster finns i dokumentationen från din registrator <a href="http://en.wikipedia.org/wiki/CNAME_record">Wikipedia posten CNAME-post</a>, eller <a href="http://tools.ietf.org/html/rfc1035">IETF domännamn - implementering och specifikation</a> dokumentet.
> 
> 

