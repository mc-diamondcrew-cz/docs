# Vývoj na DiamondCrewMC
* [Příprava prostředí](#env)
* [Vývoj serverů](#server)
* [Pravidla Commitů](#commits)

## <a name="env"> Příprava prostředí
Tento dokument popisuje jak nastavit Vaše lokální vývojové prostředí pro vytvoření a testování DiamondCrewMC herních serverů a proujektů.

### Instalování požadovaných aplikací
* [Git&Git Bash](http://git-scm.com/): Pro začátečníky doporučujeme navštívit 
  [GitHub návod pro nainstalování Gitu](https://docs.github.com/en/get-started/quickstart/set-up-git). 
  Bash používáme pro "nastarování" serverů a instalace požadovaných souborů. (Pluginy, Překlady..)
* [Java](https://www.java.com/en/): Všechny servery samozřejmě vyžadují Javu pro spuštění.

### Forkování DiamondCrewMC projektů
Abyste mohli přispívat do našich repozitorářů, je potřeba, abyste vlastnili GitHub účet pro "pushování" commitů do vlastního forku našich repozitorářů. 
Poté jen musíte otevřít Pull Request v daném GitHub Repozitoráři.

Pro vytvoření GitHub účtu, následujte instrukce [zde](https://github.com/signup/free). Poté můžete jakýkoli náš repozitořár "[forknout](help.github.com/forking)".

### Klonování repozitoráře
Pro lokální práci s našimi projekty, je potřeba je naklonovat z Vašeho forknutého repozitoráře.

Pro práci na jekémkoli z našich projektů doporučujeme [IntelliJ IDEA](https://www.jetbrains.com/idea/).

```shell
# Pro tento návod budeme předpokládat, že chceme pracovat v našem forknutém repozitoráři projektu "server-proxy".
# Proto, v případě, že pracujete s projektem jiným, všechny tyto zmínky nahraďte názvem daného projektu.

# Naklonujte Váš repozitorář:
git clone https://github.com/<github jméno>/server-proxy.git

# Běžte do nově vytvořené složky:
cd server-proxy

# Přidejte daný repozitorář jako remote upstream do Vašeho repozitoráře.
git remote add upstream "https://github.com/mc-diamondcrew-cz/server-proxy.git"
```

Toto je pouze všeobecný návod pro všechny projekty, bližší a podrobnější instrukce naleznete v sekcích pro dané oddíly. 
(Vývoj pluginů, Vývoj serverů, Vývoj webových aplikací..)

## <a name="server"> Vývoj serverů
Tento dokument popisuje potřebné kroky k tomu, abyste správně spustily jeden z našich serverů pomocí veřejného repozitoráře.

### Nastavení proměnných
Jelikož pracujeme s obecným a veřejným repozitořém, je potřeba zachovat citlivá data a proměnné skryté s možností je mít na každém prostředí jiné. 
Protože většinou pracujeme pouze s `.yml` a podobnýmy soubory, není moc možností jak tohoto dosáhnout čistou a jednoduchou cestou.

Proto jsme zvolili následující postup:
```shell
# Každý repozitorář herního serveru by měl obsahovat soubor "variables.txt.EXAMPLE", 
# v němž jsou napsaný všechny vyžadované proměnné ve formátu bash pole.

# Tento soubor zkopírujeme:
cp variables.txt.EXAMPLE variables.txt

# Poté soubor otevřeme a vyplníme požadovaná data.
vim variables.txt
```

#### Ukázka jak by měl soubor `variables.txt` přibližně vypadat před a po úpravě.
Před:
```bash
(
  ["SOME_PORT"]=""
  ["SOME_PASSWORD"]=""
  ["SOME_IP"]=""
)
```
Po:
```bash
(
  ["SOME_PORT"]="25565"
  ["SOME_PASSWORD"]="abc123654"
  ["SOME_IP"]="127.0.0.1"
)
```

**VŠECHNY POLE MUSÍ BÝT UNIKÁTNÍ A NESMÍ OBSAHOVAT ŽÁDNÉ SPECIÁLNÍ ZNAKY**

#### Práce s proměnnými
Velmi důležitá část práce s proměnnými je pamatovat si na to, že je musíte vždy vrátit do původního stavu; to znamená necommitovat do repozitoráře žádná citlivá data.

Níže jsou vysvětleny různé konfigurace pro spouštění `.sh` souborů, jedním z nich je také konfigurace `Var-STOP`, kterou byste měli vždy před commitem spustit.

#### Formát proměnných
Jak je již vysvětleno výše, proměnné jsou uložený v poli.

Následně nastavená hodnota: `["NAME"]="Value"` by mohla být použita v každém konfiguračním souboru jako `{$NAME}`.

### Konfigurace pro jednodušší práci s projektem
Použitím například IntelliJ IDEA věstavěného systému konfigurací si můžeme ulehčit práci na serveru.

#### Doporučené konfigurace
| Name            | Path                         | Options               | Working Directory | Description                                                   |
|-----------------|------------------------------|-----------------------|-------------------|---------------------------------------------------------------|
| Start           | `$PROJECT_DIR$/start.sh`     | 4096 false false      | `$PROJECT_DIR$`   | Start server with 4GB RAM                                     |
| Start-ALL       | `$PROJECT_DIR$/start.sh`     | 4096 true true        | `$PROJECT_DIR$`   | Download locales, latest server and start server with 4GB RAM |
| Start-JAR       | `$PROJECT_DIR$/start.sh`     | 4096 true false       | `$PROJECT_DIR$`   | Download latest server and start server with 4GB RAM          |
| Start-LANG      | `$PROJECT_DIR$/start.sh`     | 4096 false true       | `$PROJECT_DIR$`   | Download locales and start server with 4GB RAM                |
| Start-PL        | `$PROJECT_DIR$/start.sh`     | 4096 false false true | `$PROJECT_DIR$`   | Downloads plugins and start server with 4GB RAM               |
| Var-START       | `$PROJECT_DIR$/variables.sh` | start                 | `$PROJECT_DIR$`   | Replaces variables with configured values                     |
| Var-STOP        | `$PROJECT_DIR$/variables.sh` | stop                  | `$PROJECT_DIR$`   | Replaces variable values with their names                     |
| Language        | `$PROJECT_DIR$/language.sh`  |                       | `$PROJECT_DIR$`   | Download locales                                              |
| Plugins         | `$PROJECT_DIR$/plugins.sh`   |                       | `$PROJECT_DIR$`   | Downloads required plugins                                    |
  
## <a name="commits"> Pravidla Commitů
Pro přehledný repozitář, ve kterém se dá jednoduše pohybovat a rychlé nacházení a opravování chyb v budoucnu je nutné vést přehledný systém commitů.
  
### Formát Commit Zpráv
Každý commit by se skládá z **hlavičky**, **těla** a **patičky**. Hlavička má speciální formát, který obsahuje **typ**, **rozsah** a **předmět**.
Každý commit je zároveň psán výhradně v anglickém jazyce.

```
<typ>(<rozsah>): <předmět>
<PRÁZDNÁ LINKA>
<tělo>
<PRÁZDNÁ LINKA>
<patička>
```
**Hlavička** je povinná a **rozsah** hlavičky je nepovinný.
  
Žádná linka commit zprávy nesmí být delší 100 znaků! Toto napomáhá čitelnosti commitů a také různým Git nástrojům.

### Revert
Pokud commit vrací zpět předchozí commit, měl by začínat s `revert: `, kdy následuje hlavička navráceného commitů. 
V tělu by mělo být napsáno `This reverts commit <hash>.`, kde hash je SHA navráceného commitu.

### Typ
Musí být jedno z následujících:
* **feat**: Nová funkce
* **fix**: Oprava chyby
* **docs**: Úprava týkající se pouze dokumentace
* **style**: Změna, která se netýká změny kódu (white-space, formátování, chybějící středník, apod)
* **refactor**: Změna kódu, která neopravuje žádnou chybu a nepřidává žádnou funkci
* **perf**: Změna kódu, která zlepšuje výkon
* **test**: Přidávání chybějící, nebo oprava existujících testů
* **chore**: Změna, nebo úprava build-scriptů.

### Rozsah
Rozsah může být cokoliv označující místo změny, kterou commit zasahuje. Například název pluginu, třídy apod..
  
Můžete použít `*`, když změna zasahuje vetší rozsah.

### Předmět
Předmět obsahuje stručný popis změny:
* použijte rozkazovací způsob v přítomném čase: "change", nikoliv "changed" nebo "changes".
* nezáčínejte velkým písmenem
* žádná tečka (.) na konci
  
### Tělo
Platí stejná pravidla jako pro **předmět**. Tělo by mělo obsahovat motivaci ke změně a porovnat ji s předchozím chováním.

### Patička
Patička by měla obsahovat veškeré informace o **Ničivých Změnách** a je také místem pro 
[odkaz na issues GitHubu, které tento commit uzavírá](https://help.github.com/articles/closing-issues-via-commit-messages/).
