Azure fastställer vilken Pythonversion det ska använda för sin virtuella miljö med följande prioritet:

1. versionen som anges i runtime.txt i rotmappen
1. versionen som anges i Python-inställningen i webbappkonfigurationen (**Inställningar** > **Programinställningar**-bladet för din webbapp i Azure Portal)
1. python-2.7 är standard om inget av ovanstående har angetts

Giltiga värden för innehållet i 

    \runtime.txt

är:

- python-2.7
- python-3.4

Om mikroversion (tredje siffran) har angetts, ignoreras den.



<!--HONumber=Jun16_HO2-->


