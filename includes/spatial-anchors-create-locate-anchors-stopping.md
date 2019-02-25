## <a name="pausing-resetting-or-stopping-the-session"></a>Pausar, återställa eller stoppar sessionen

Om du vill stoppa sessionen tillfälligt, kan du anropa metoden Stop. Då slutar att alla Övervakare och bearbetning av miljön, även om du anropar ProcessFrame(). Du kan sedan anropa Start() för att återuppta bearbetningen. När de återställs, underhålls miljödata som redan hämtats i sessionen.
