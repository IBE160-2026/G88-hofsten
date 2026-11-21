# KI-logg

Bruk denne fila til å notere KI-samtaler som påvirker prosjektet. Behold gamle notater, og legg nye økter nederst.

## Kopier denne delen for hver økt

### Dato: [dd.mm.åååå]

**KI-verktøy:** [f.eks. GitHub Copilot]

**Hva prøvde jeg å gjøre?**
[Skriv kort]

**Prompten jeg skrev:**
> [Lim inn prompten akkurat slik du skrev den]

**Hva foreslo KI?**
[Skriv kort, eller lim inn den viktige delen av svaret]

**Hva valgte jeg å gjøre?**
[Skriv hva du brukte, endret eller lot være å bruke]

**Hvordan sjekket jeg det?**
[Skriv hvordan du testet eller kontrollerte resultatet]

---

### Dato: 17.11.2026

**KI-verktøy:** GitHub Copilot

**Hva prøvde jeg å gjøre?**
Oppdatere Product Briefen med den godkjente, simulerte drivstoffmodellen, bevare full testfasit i BMAD-spesifikasjonen og fullføre arbeidsflyten uten å inkludere eksisterende, uvedkommende endringer.

**Prompten jeg skrev:**
> Du kan nå stage og committe kun disse to filene, og fullføre BMAD one-shot-arbeidsflyten.

**Hva foreslo KI?**
KI foreslo å holde Product Briefen på produktnivå, beholde en kort sammenligningstabell og legge detaljerte formler og håndregninger i spesifikasjonen. Reviewene pekte blant annet på tydeliggjøring av tidsgrunnlag, avrunding og scenarioet som treffer kaivinduet.

**Hva valgte jeg å gjøre?**
Godkjente den avgrensede dokumentendringen og commit av kun `product-brief.md` og `.docs/implementation-artifacts/spec-voyageai-cost-model.md`. Tidligere arbeidskopiendringer ble stående urørt. Ingen push ble gjort.

**Hvordan sjekket jeg det?**
Etterregnet drivstoff og kostnader som eksakte brøker, kontrollerte filene og diffen, triagerte eksisterende reviewfunn og verifiserte med `git show` at commit `d8eb52d4ad14d3fbcf7876e19232fee91b18c97a` bare inneholder de to godkjente filene.

---

### Dato: 18.11.2026

**KI-verktøy:** GitHub Copilot

**Hva prøvde jeg å gjøre?**
Kontrollere Product Briefen punktvis mot foreleserens tilbakemelding og sensorveiledningen, gjøre de godkjente minimumspresiseringene, publisere kun den ferdigstilte briefen, og dokumentere arbeidet i KI- og sesjonsloggen.

**Viktige promptutdrag:**
> «Vi skal nå avslutte Product Brief-fasen i VoyageAI før vi går videre til PRD. [...] Ikke gjør endringer ennå. Gi meg en kort rapport med anbefalte minimumsendringer.»
>
> «Gjennomfør kun de fem nødvendige, avgrensede presiseringene fra rapporten» og «Ikke endre kostnadsmodellen, formlene eller de håndkontrollerte scenarioresultatene.»
>
> «Jeg godkjenner de fem presiseringene i Product Briefen» og ba om commit av kun `product-brief.md` samt push til riktig repository og branch.
>
> «Før vi avslutter dagens IBE160-økt, vil jeg kontrollere dokumentasjonen» og ba om at kun KI-logg og relevant sesjonslogg oppdateres, uten commit eller push før godkjenning.

**Hva foreslo KI?**
Sluttkontrollen fant at kostnadsmodell og prøvereise fra 17. november allerede var dekket. Den pekte på manglende nøkkelfri mock-forklaring, for bred primærbruker, manglende kort differensiering og visjon, samt en addendum-henvisning som ikke pekte til faktisk plassering. Den skilte disse brief-presiseringene fra README, implementasjon, UX-skisser og andre forhold som hører til senere arbeid.

**Hva valgte jeg å gjøre?**
Godkjente bare de fem avgrensede presiseringene. Lot kostnadsmodell og scenariotall stå uendret og lot PRD og programmering vente til separat planlegging. Godkjente deretter commit og push kun av `product-brief.md`. For loggoppdateringen ba jeg om kun å endre KI-logg og relevant sesjonslogg, uten commit eller push før jeg har godkjent resultatet.

**Hvordan sjekket jeg det?**
Kontrollerte diffen og `git diff --check`; bekreftet at bare `product-brief.md` var med i commit `3ed0368188aeae78e2b0bf6f0ba78ca20122c1ea`, og at `origin/main` pekte på samme commit. Andre endrede og uregistrerte filer ble stående utenfor.

---

### Dato: 19.11.2026

**KI-verktøy:** GitHub Copilot i VS Code (ingen separat Claude-kjøring er dokumentert)

**Hva prøvde jeg å gjøre?**
Ferdigstille, validere og godkjenne VoyageAI PRD for den avgrensede V1-prøvereisen, dokumentere beslutninger og testgrunnlag, og publisere PRD-pakken til GitHub. Arbeidet omfattet to separate BMAD PRD Validate-runder, retting av valideringsfunn, en regel for KI-anbefaling og en godkjent, kanonisk fallback-forklaring. Ingen implementering ble startet.

**Promptene jeg skrev:**
Nedenfor er korte, ordrette utdrag fra arbeidsdialogen, ikke en full prompttranskripsjon. De øvrige promptdetaljene er ikke lagret som en egen eksportert samtalefil.

> «Før vi godkjenner eller endrer PRD-en, ønsker jeg å gjennomføre en kontroll av MVP-omfanget opp mot den opprinnelige Product Briefen og foreleserens tilbakemelding.»

Dette ledet til en dokumentbasert vurdering av første Brief-utkast, tilbakemeldingen og sensorveiledningen. Resultat og V1-avgrensning er sporet i [PRD-en](.docs/planning-artifacts/prds/prd-VoyageAI-2026-11-18/prd.md), særlig scope- og ikke-målseksjonene, og i [.memlog.md](.docs/planning-artifacts/prds/prd-VoyageAI-2026-11-18/.memlog.md).

> «Kjør en ny, separat BMAD PRD Validate på gjeldende PRD.»

Valideringsbestillingen ba om helhetlig kontroll av krav, kostnadsmodell, API-fallback, IBE160-prosessmål, omfang og testbarhet, uten automatisk PRD-endring. Den nye rapporten er [validation-report.md](.docs/planning-artifacts/prds/prd-VoyageAI-2026-11-18/validation-report.md), og full rubric-vurdering ligger i [review-rubric.md](.docs/planning-artifacts/prds/prd-VoyageAI-2026-11-18/review-rubric.md).

> «Vi har nå tatt de siste produktbeslutningene for VoyageAI V1.»

Brukeren fastsatte én A–B–C-prøvereise, redigerbar forsinkelse 0–720 minutter, faste scenariohastigheter og API-fallback. Senere besluttet brukeren prioriteringsregelen: bruk ETA ved C for «Keep 10 knots» som referanse, velg blant scenarioene som ankommer senest da det med lavest uavrundet simulert drivstoffkostnad, og bryt kostnadslikhet med tidligste ETA. Dette var brukerens produktbeslutning, ikke en KI-oppfunnet regel; den er ført i memloggen og FR-6/FR-7.

> «eg godkjenner den foreslåtte kanoniske mock-forklaringen for 12-timersprøven, med én liten presisering:»

Brukeren godkjente den foreslåtte mocken og krevde at den tydelig viser at anbefalingen følger den forhåndsdefinerte prioriteringsregelen. Den endelige teksten er lagret som [mock-12-hour.md](.docs/planning-artifacts/prds/prd-VoyageAI-2026-11-18/mock-12-hour.md); FR-7 viser dit som ordrett testfasit.

Til slutt ba brukeren om commit og push med meldingen `docs: approve VoyageAI V1 PRD`; committen og filomfanget kan etterprøves i [GitHub-commit 49eaf9b7ea3499d5466bf8fde28c8d2b9221c2a5](https://github.com/IBE160-2026/G88-hofsten/commit/49eaf9b7ea3499d5466bf8fde28c8d2b9221c2a5).

**Hva foreslo KI?**
KI hjalp med å sammenligne den opprinnelige produktideen med den reviderte Briefen, strukturere PRD-krav og akseptansekriterier, og utlede testbare eksempler fra den godkjente kostnadsmodellen. Første Validate ga rubric-karakteren **Good** og fant fire middels og tre lave forhold: manglende fallback-testfasit, svekket krav om AI-anbefaling, manglende sporbarhet for IBE160-målet, manglende kostnadsoracle for endret forsinkelse, foreldet dato, uobserverbart SM-3 og ufullstendige avrundings-/klokkeregler. KI foreslo rettinger; brukeren godkjente V1-beslutningene og mock-teksten.

Første Validate-resultat (**Good**, fire middels og tre lave funn) er bevart som en hendelse i [BMAD-beslutningsloggen](.docs/planning-artifacts/prds/prd-VoyageAI-2026-11-18/.memlog.md); den opprinnelige rapporten ble senere erstattet av andre Validate. Andre Validate fant at de sju funnene var håndtert og ga **Excellent**, uten kritiske, høye eller middels funn, men med ett lavt funn om ulike scenarioetiketter i FR-5-tabellen. Den gjeldende [valideringsrapporten](.docs/planning-artifacts/prds/prd-VoyageAI-2026-11-18/validation-report.md) og [rubric-reviewen](.docs/planning-artifacts/prds/prd-VoyageAI-2026-11-18/review-rubric.md) dokumenterer dette. Brukeren ba om ensretting og godkjente deretter PRD-en. KI fant også at eldre reconciliation-notater var utdaterte; de ble ikke inkludert i PRD-commiten.

**Hva valgte jeg å gjøre?**
Brukeren tok de selvstendige produktbeslutningene: V1 er én simulert A–B–C-prøvereise; brukeropprettede og flere prøvereiser ble skjøvet til mulige framtidige utvidelser av hensyn til gjennomførbarhet, testbarhet og kurstidsramme. Brukeren fastsatte de tre A–B-hastighetene, forsinkelsesintervallet, reglene for ugyldig input og ny beregning fra grunnlagsdata, samt den balanserte anbefalingsregelen for tid og kostnad.

Brukeren godkjente også skillet mellom produktkrav og IBE160-prosessmål, API-fallbacken, den kanoniske mock-forklaringen og til slutt PRD-statusen `approved`. KI-forslaget om mock ble ikke behandlet som godkjent før brukeren godkjente teksten. Den første lavprioriterte scenarioetikett-variasjonen ble rettet før godkjenning.

**Mulig refleksjonspunkt:** KI bidro med strukturering av krav, konsistenskontroller og konkrete testforslag, men flere produktvalg måtte avgjøres av brukeren. De to Validate-rundene viste verdien av å behandle KI-utkast som forslag som må sammenholdes med godkjente kilder; de fant også mangler som ikke var synlige i første utkast. Arbeidet kontrollerte dokumenter og beregninger, men omfattet ikke kjørende applikasjons- eller implementasjonstester.

**Hvordan sjekket jeg det?**
- **Validate 1:** BMAD-rubricen ga Good med 0 kritiske, 0 høye, 4 middels og 3 lave funn. Rapporten og rubric-fila ligger i PRD-run-mappen.
- **Validate 2:** BMAD-rubricen ga Excellent med 0 kritiske, 0 høye, 0 middels og 1 lavt funn. Kildeavstemmingen bekreftet at de tolv-timers tallene i den kanoniske mocken samsvarer med FR-5.
- **Beregninger:** KI kontrollerte ETA og venting ved 0, 6 og 12 timer. For det ekstra 6-timerstilfellet ble drivstoff og kostnader utledet fra [kostnadsmodellens formler](.docs/implementation-artifacts/spec-voyageai-cost-model.md): Keep 10 knots = 45.500 t / USD 27,300.00; Increase to 12.5 knots = 61.875 t / USD 37,125.00; Reduce to 100/11 knots on A–B = 40.043388 t / USD 24,026.03. Den godkjente [12-timersfasiten i PRD-en](.docs/planning-artifacts/prds/prd-VoyageAI-2026-11-18/prd.md) og kostnadsspesifikasjonen ble ikke endret. Dette var dokument-/beregningskontroll, ikke kjøring av implementasjonstester.
- **Krav og sporbarhet:** Kontrollerte FR-6/FR-7 mot samme prioriteringsregel, fallback mot gjeldende beregnede resultater, IBE160 SP-1 mot Product Brief-kriterium 4, og endelig V1-scope mot memlog og sensorveiledning.
- **Grunnlag for refleksjon:** Kontrollerte at oppføringen dekker promptutdrag, KI-forslag, egne beslutninger og kvalitetssikring som etterspurt i [refleksjonsrapportmalen](course-guidance/reflection-report-template.md), og at prosessporbarhet/testing er knyttet til [sensorveiledningen for del 1](course-guidance/Sensorveiledning%20IBE160%20-%20Del%201%20applikasjon%20og%20prosess.pdf). Den detaljerte øktshistorikken finnes i [sesjonsloggen](.logs/victoria-hofsten/997-2026-11-18-14-10-voyageai-prd.md).
- **Publisering:** Kjørte `git diff --check`, kontrollerte lenker, undersøkte utvalgte filer for vanlige hemmelighetsmønstre, bekreftet at Product Brief, addendum og kostnadsmodell ikke var med, og kontrollerte at commit `49eaf9b7ea3499d5466bf8fde28c8d2b9221c2a5` ble pushet til `origin/main`.

KI-loggen ble ikke inkludert i PRD-commiten eller pushet. Denne 19.11-oppføringen er lagt til lokalt etterpå; den venter fortsatt på separat godkjenning før eventuell publisering.

---

### Dato: 21.11.2026 (arkitekturarbeid 20.–21.11)

**KI-verktøy:** GitHub Copilot i VS Code med BMAD Architecture og uavhengige reviewer-agenter

**Hva prøvde jeg å gjøre?**
Ferdigstille arkitekturspinen for VoyageAI V1 før implementering. Målet var å kontrollere at valgt modulær monolitt følger den godkjente PRD-en og samtidig gjøre beregninger og anbefalingslogikk testbare uten KI-tjenesten.

**Prompten jeg skrev:**
> «Jeg velger en modulær monolitt for VoyageAI V1. Det viktigste for meg er enkelhet, testbarhet, vedlikeholdbarhet og å levere en fungerende prototype innen eksamensfristen.»
>
> «Arkitekturen må følge den godkjente PRD-en. Beregninger og anbefalinger skal være deterministiske og kunne testes uavhengig av KI-tjenesten. Vurder gjerne om det finnes konkrete krav i PRD-en som taler mot dette valget, før vi dokumenterer beslutningen.»

Jeg valgte BMAD **Coaching path** og architecture spine som eneste leveranse. I oppfølgingsvalgene godkjente jeg Flask, server-rendert Jinja, lokal demo og Python 3.14+; den endelige spinen avgrenser førstestøtten til CPython 3.14.x.

**Hva foreslo KI?**
KI vurderte kravene i PRD-en opp mot arkitekturvalget og fant ikke krav om separat deployering, API-tjeneste, database, kontoer eller produksjonsdrift. KI anbefalte én deploybar Flask-app med tydelige in-process grenser mellom presentasjon, applikasjonsflyt, domeneberegning, anbefalingspolicy og valgfri KI-adapter.

Uavhengige reviewere fant først mangler i felles resultatkontrakt, eksakt presisjon, vindus-/hastighetsregler, fallback-merking og -ruting, synlige simulated-data- og beslutningsgrenser, provider-validering og testdekning. Product Brief-reviewen pekte også på 36-timers-eksempel og AI-eid anbefaling; dette ble vurdert som eldre tekst som er avgrenset/erstattet av godkjent PRD og brukerens beslutning om deterministisk policy.

**Hva valgte jeg å gjøre?**
Jeg valgte modulær monolitt, Flask/Jinja og lokal kjøring. Domenelogikk beregner scenariene og velger anbefalingen deterministisk fra uavrundede verdier; KI får bare forklare resultatet. KI kan ikke beregne eller endre tall, velge et annet scenario eller overstyre brukerens beslutningsansvar. Manglende, feilende eller ugyldig KI-svar går til PRD-definert mock eller regelbasert fallback.

Reviewfunnene ble innarbeidet i spinen, inkludert immutable `ComparisonResult`/`ScenarioResult`, exact `100/11`-fart, grense- og avrundingsregler, synlige forklaringsetiketter, provider-validering og testmatrise. Python-støtte ble avgrenset til 3.14.x inntil nyere minorversjoner er testet. Provider, modell og konkrete tidsfrister ble utsatt til adapter-implementering. PRD, Product Brief og applikasjonskode ble ikke endret.

**Hvordan sjekket jeg det?**
- Kjørte architecture-spinelinteren etter endringene; sluttresultat: 0 funn.
- Kontrollerte Python 3.14.10, Flask 3.1.3 og uv 0.13.9 mot offisielle kilder; stack-/operations-reviewen fant ingen kompatibilitetsblokkering.
- Final adversarial review fant ingen gjenværende grensehull. Rubric-funnet om Python 3.15 ble rettet. Kildeavstemming og reviewerfunn er bevart under [architecture reviews](.docs/planning-artifacts/architecture/architecture-VoyageAI-2026-11-20/reviews/).
- Et siste separat stack-review-kall feilet med 401, så ingen egen `review-final-stack.md` ble laget. Versjoner og fit var allerede verifisert i tidligere review; dette er en begrensning i siste reviewrunde.
- Det finnes ikke applikasjonskode ennå, så ingen applikasjons- eller beregningstester ble kjørt. Dette var arkitektur- og dokumentkontroll.

---
