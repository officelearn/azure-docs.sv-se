


**Dokumentera senaste uppdatering**: 22 januari 3:00 PM PST.

Senaste avslöjande av en [ny klass för CPU-säkerhetsrisker](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) spekulativ exekvering sida-kanal attacker som kallas har resulterat i frågor från kunder som vill ha mer tydlighets skull.  

Den infrastruktur som kör Azure och isolerar kundens arbetsbelastningar från varandra är skyddad.  Detta innebär att andra kunder som körs på Azure inte kan angrepp ditt program med hjälp av dessa problem.

> [!NOTE] 
> Azure åtgärder tidigare annonserats på 3 januari 2018 påverkas inte av den senaste [uppdaterade riktlinjer](https://newsroom.intel.com/news/root-cause-of-reboot-issue-identified-updated-guidance-for-customers-and-partners/) från Intel. Det görs inga ytterligare underhållsåtgärd på kundens virtuella datorer på grund av den här nya informationen.
>
> Vi kommer att fortsätta att uppdatera tipsen som vi får mikrokod uppdateringar från maskinvarutillverkare. Kontrollera uppdaterade riktlinjer för.
>

## <a name="keeping-your-operating-systems-up-to-date"></a>Uppdatera ditt operativsystem kontinuerligt

När en OS-uppdatering inte krävs för att isolera dina program som körs på Azure från andra kunder som körs på Azure, men det är alltid en bra idé att hålla OS-versioner som är uppdaterade. 

I följande erbjudanden följer våra rekommenderade åtgärder för att uppdatera operativsystemet: 

<table>
<tr>
<th>Erbjudande</th> <th>Rekommenderad åtgärd </th>
</tr>
<tr>
<td>Azure Cloud Services </td>  <td>Aktivera automatisk uppdatering eller se till att du kör den senaste gäst-OS.</td>
</tr>
<tr>
<td>Virtuella Azure Linux-datorer</td> <td>Installera uppdateringar från leverantören av operativsystemet när det är tillgängligt. </td>
</tr>
<tr>
<td>Windows Azure virtuella datorer </td> <td>Kontrollera att du kör ett program som stöds antivirusprogram innan du installerar uppdateringar av operativsystem. Kontakta din antivirusprogramleverantör information om kompatibilitet.<p> Installera den [januari insamling av säkerhet](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002). </p></td>
</tr>
<tr>
<td>Andra Azure PaaS-tjänster</td> <td>Det finns ingen åtgärd krävs för kunder som använder dessa tjänster. Azure håller automatiskt OS-versioner aktuella. </td>
</tr>
</table>

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>Mer information om du använder icke betrodd kod 

Ingen ytterligare kunden åtgärd krävs om du inte kör icke betrodd kod. Om du tillåter kod som du inte litar (till exempel du tillåter en av dina kunder att överföra en binär eller kodstycke som du kör i molnet i ditt program), och sedan följande åtgärder vidtas.  


### <a name="windows"></a>Windows 
Om du använder Windows och värd icke betrodd kod, bör du även aktivera en Windows-funktion som kallas Kernel virtuell adress (KVA) skuggning som ger ytterligare skydd mot spekulativ exekvering sida-kanal säkerhetsproblem. Den här funktionen är inaktiverad som standard och kan påverka prestanda om aktiverat. Följ [Windows Server KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) instruktioner för att aktivera skydd på servern. Om du använder Azure Cloud Services, kontrollera att du kör WA-GUEST-OS-5.15_201801-01 eller WA-GÄST-OS-4.50_201801-01 (tillgänglig från den 10 januari) och aktivera registret nyckel via en startaktivitet.


### <a name="linux"></a>Linux
Om du kör Linux och värd icke betrodd kod kan uppdatera du även Linux till en nyare version som implementerar kernel sidan tabell isolering (KPTI) som särskiljer sidan-tabeller som används av kernel från de som tillhör användaren utrymme. Dessa åtgärder kräver en Linux OS-uppdatering och kan hämtas från leverantören av distributionsplatsen när det är tillgängligt. OS-providern kan berätta om skydd är aktiverade eller inaktiverade som standard.



## <a name="next-steps"></a>Nästa steg

Läs mer i [skydda Azure-kunder från CPU säkerhetsproblem](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/).
