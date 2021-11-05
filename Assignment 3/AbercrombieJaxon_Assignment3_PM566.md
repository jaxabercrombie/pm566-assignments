### APIs

To find papers under the search term of “sars-cov-2 trial vaccine,” the
following code was produced, and the number of results were counted:

    website <- xml2::read_html("https://pubmed.ncbi.nlm.nih.gov/?term=sars-cov-2+trial+vaccine")

    counts <- xml2::xml_find_first(website, "/html/body/main/div[9]/div[2]/div[2]/div[1]/div[1]/span")

    counts <- as.character(counts)

Based on the search term(s) “sars-cov-2 trial vaccine,” there were 2,336
papers found.

To download paper details for the first 250 papers and format the IDs
sourced from the code:

    query_ids <- GET(
          url   = "https://eutils.ncbi.nlm.nih.gov/",
          path = "entrez/eutils/esearch.fcgi",
          query = list(db       = "pubmed",
                       term     = "sars-cov-2 trial vaccine",
                       rettype = "abstract",
                       retmax   = 250)
        )

    ids <- httr::content(query_ids)
    ids <- as.character(ids)
    ids <- stringr::str_extract_all(ids, "<Id>[0-9]+</Id>")[[1]]
    ids <- stringr::str_remove_all(ids, "<Id>|</Id>")

To then create a dataset for the first 250 papers and their information
about ID \#, title, journal name, publication date, and abstract:

    publications <- GET(
      url = "https://eutils.ncbi.nlm.nih.gov/",
      path = "entrez/eutils/efetch.fcgi",
      query = list(
        db = "pubmed",
        id = paste(ids, collapse = ","),
        retmax = 250,
        rettype = "abstract"
        ))
    publications <- httr::content(publications)
    publications_txt <- as.character(publications)

    pub_char_list <- xml2::xml_children(publications)
    pub_char_list <- sapply(pub_char_list, as.character)
        
    # From Lab 7, and the other variables are modeled after the next three lines of code
    abstracts <- str_extract(pub_char_list, "<Abstract>[[:print:][:space:]]+</Abstract>")
    abstracts <- str_remove_all(abstracts, "</?[[:alnum:]- =\"]+>")
    abstracts <- str_replace_all(abstracts, "[[:space:]]+", " ")

    # For article titles...
    titles <- str_extract(pub_char_list, "<ArticleTitle>[[:print:][:space:]]+</ArticleTitle>")
    titles <- str_remove_all(titles, "</?[[:alnum:]- =\"]+>")
      
    # For journal titles...
    journals <- str_extract(pub_char_list, "<Title>[[:print:][:space:]]+</Title>")
    journals <- str_remove_all(journals, "</?[[:alnum:]- =\"]+>") 
    journals <- str_replace_all(journals, "[[:space:]]+", " ")
      
    # For publciation dates
    dates <- str_extract(pub_char_list, "<PubDate>[[:print:][:space:]]+</PubDate>")
    dates <- str_remove_all(dates, "</?[[:alnum:]- =\"]+>")
    dates <- str_replace_all(dates, "[[:space:]]+", " ")

    database <- data.frame(
          PubMedID = ids,
          Title = titles,
          Journal = journals,
          PubDate = dates,
          Abstract = abstracts
        )

    knitr::kable(database[1:20, ], caption = "Twenty papers about SARS-CoV-2 Trial Vaccines")

<table>
<caption>Twenty papers about SARS-CoV-2 Trial Vaccines</caption>
<colgroup>
<col style="width: 0%" />
<col style="width: 6%" />
<col style="width: 3%" />
<col style="width: 0%" />
<col style="width: 88%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;">PubMedID</th>
<th style="text-align: left;">Title</th>
<th style="text-align: left;">Journal</th>
<th style="text-align: left;">PubDate</th>
<th style="text-align: left;">Abstract</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;">34735795</td>
<td style="text-align: left;">Immunogenicity of standard and extended dosing intervals of BNT162b2 mRNA vaccine.</td>
<td style="text-align: left;">Cell</td>
<td style="text-align: left;">2021 Oct 16</td>
<td style="text-align: left;">Extension of the interval between vaccine doses for the BNT162b2 mRNA vaccine was introduced in the United Kingdom to accelerate population coverage with a single dose. At this time, trial data were lacking, and we addressed this in a study of United Kingdom healthcare workers. The first vaccine dose induced protection from infection from the circulating alpha (B.1.1.7) variant over several weeks. In a substudy of 589 individuals, we show that this single dose induces severe acute respiratory syndrome coronavirus 2 (SARS-CoV-2) neutralizing antibody (NAb) responses and a sustained B and T cell response to the spike protein. NAb levels were higher after the extended dosing interval (6-14 weeks) compared with the conventional 3- to 4-week regimen, accompanied by enrichment of CD4+ T cells expressing interleukin-2 (IL-2). Prior SARS-CoV-2 infection amplified and accelerated the response. These data on dynamic cellular and humoral responses indicate that extension of the dosing interval is an effective immunogenic protocol. Copyright © 2021 The Author(s). Published by Elsevier Inc. All rights reserved.</td>
</tr>
<tr class="even">
<td style="text-align: left;">34735426</td>
<td style="text-align: left;">Effectiveness of 2-Dose Vaccination with mRNA COVID-19 Vaccines Against COVID-19-Associated Hospitalizations Among Immunocompromised Adults - Nine States, January-September 2021.</td>
<td style="text-align: left;">MMWR. Morbidity and mortality weekly report</td>
<td style="text-align: left;">2021 Nov 05</td>
<td style="text-align: left;">Immunocompromised persons, defined as those with suppressed humoral or cellular immunity resulting from health conditions or medications, account for approximately 3% of the U.S. adult population (1). Immunocompromised adults are at increased risk for severe COVID-19 outcomes (2) and might not acquire the same level of protection from COVID-19 mRNA vaccines as do immunocompetent adults (3,4). To evaluate vaccine effectiveness (VE) among immunocompromised adults, data from the VISION Network* on hospitalizations among persons aged ≥18 years with COVID-19-like illness from 187 hospitals in nine states during January 17-September 5, 2021 were analyzed. Using selected discharge diagnoses,† VE against COVID-19-associated hospitalization conferred by completing a 2-dose series of an mRNA COVID-19 vaccine ≥14 days before the index hospitalization date§ (i.e., being fully vaccinated) was evaluated using a test-negative design comparing 20,101 immunocompromised adults (10,564 [53%] of whom were fully vaccinated) and 69,116 immunocompetent adults (29,456 [43%] of whom were fully vaccinated). VE of 2 doses of mRNA COVID-19 vaccine against COVID-19-associated hospitalization was lower among immunocompromised patients (77%; 95% confidence interval [CI] = 74%-80%) than among immunocompetent patients (90%; 95% CI = 89%-91%). This difference persisted irrespective of mRNA vaccine product, age group, and timing of hospitalization relative to SARS-CoV-2 (the virus that causes COVID-19) B.1.617.2 (Delta) variant predominance in the state of hospitalization. VE varied across immunocompromising condition subgroups, ranging from 59% (organ or stem cell transplant recipients) to 81% (persons with a rheumatologic or inflammatory disorder). Immunocompromised persons benefit from mRNA COVID-19 vaccination but are less protected from severe COVID-19 outcomes than are immunocompetent persons, and VE varies among immunocompromised subgroups. Immunocompromised persons receiving mRNA COVID-19 vaccines should receive 3 doses and a booster, consistent with CDC recommendations (5), practice nonpharmaceutical interventions, and, if infected, be monitored closely and considered early for proven therapies that can prevent severe outcomes.</td>
</tr>
<tr class="odd">
<td style="text-align: left;">34735018</td>
<td style="text-align: left;">COVID-19 Therapeutics and Vaccines: A Race to save Lives.</td>
<td style="text-align: left;">Toxicological sciences : an official journal of the Society of Toxicology</td>
<td style="text-align: left;">2021 Nov 04</td>
<td style="text-align: left;">COVID-19 (Coronavirus Disease 2019), the disease caused by SARS-CoV-2 (Severe Acute Respiratory Syndrome Coronavirus-2) is an ongoing global public health emergency. As understanding of the health effects of COVID-19 have improved, companies and agencies worldwide have worked together to identify therapeutic approaches, fast-track clinical trials and pathways for emergency use, and approve therapies for patients. This work has resulted in therapies that not only improve survival, reduce time of hospitalization and time to recovery, but also include preventative measures, such as vaccines. This manuscript discusses development programs for three products that are approved or authorized for emergency use at the time of writing: VEKLURY (remdesivir, direct acting antiviral from Gilead Sciences, Inc.), REGEN-COV (casirivimab and imdevimab antibody cocktail from Regeneron Pharmaceuticals Inc.) and Comirnaty (Pfizer-BioNTech COVID-19 Vaccine [Pfizer, Inc.-BioNTech]), and perspectives from the US Food and Drug Administration (FDA). Published by Oxford University Press 2021.</td>
</tr>
<tr class="even">
<td style="text-align: left;">34729549</td>
<td style="text-align: left;">Adverse events of active and placebo groups in SARS-CoV-2 vaccine randomized trials: A systematic review.</td>
<td style="text-align: left;">The Lancet regional health. Europe</td>
<td style="text-align: left;">2021 Oct 28</td>
<td style="text-align: left;">For safety assessment in clinical trials, adverse events (AEs) are reported for the drug under evaluation and compared with AEs in the placebo group. Little is known about the nature of the AEs associated with clinical trials of SARS-CoV-2 vaccines and the extent to which these can be traced to nocebo effects, where negative treatment-related expectations favor their occurrence. In our systematic review, we compared the rates of solicited AEs in the active and placebo groups of SARS-CoV-2 vaccines approved by the Western pharmaceutical regulatory agencies.We implemented a search strategy to identify trial-III studies of SARS-CoV-2 vaccines through the PubMed database. We adopted the PRISMA Statement to perform the study selection and the data collection and identified three trial: two mRNA-based (37590 participants) and one adenovirus type (6736 participants). Relative risks showed that the occurrence of AEs reported in the vaccine groups was higher compared with the placebo groups. The most frequently AEs in both groups were fatigue, headache, local pain, as injection site reactions, and myalgia. In particular, for first doses in placebo recipients, fatigue was reported in 29% and 27% in BNT162b2 and mRNA-1273 groups, respectively, and in 21% of Ad26.COV2.S participants. Headache was reported in 27% in both mRNA groups and in 24% of Ad26.COV2.S recipients. Myalgia was reported in 10% and 14% in mRNA groups (BNT162b2 and mRNA-1273, respectively) and in 13% of Ad26.COV2.S participants. Local pain was reported in 12% and 17% in mRNA groups (BNT162b2 and mRNA-1273, respectively), and in 17% of Ad26.COV2.S recipients. These AEs are more common in the younger population and in the first dose of placebo recipients of the mRNA vaccines. Our results are in agreement with the expectancy theory of nocebo effects and suggest that the AEs associated with COVID-19 vaccines may be related to the nocebo effect. Fondazione CRT - Cassa di Risparmio di Torino, IT (grant number 66346, “GAIA-MENTE” 2019). © 2021 The Authors.</td>
</tr>
<tr class="odd">
<td style="text-align: left;">34726743</td>
<td style="text-align: left;">Analysis of the Effectiveness of the Ad26.COV2.S Adenoviral Vector Vaccine for Preventing COVID-19.</td>
<td style="text-align: left;">JAMA network open</td>
<td style="text-align: left;">2021 11 01</td>
<td style="text-align: left;">Continuous assessment of the effectiveness and safety of the US Food and Drug Administration-authorized SARS-CoV-2 vaccines is critical to amplify transparency, build public trust, and ultimately improve overall health outcomes. To evaluate the effectiveness of the Johnson &amp; Johnson Ad26.COV2.S vaccine for preventing SARS-CoV-2 infection. <AbstractText Label="Design, Setting, and Participants">This comparative effectiveness research study used large-scale longitudinal curation of electronic health records from the multistate Mayo Clinic Health System (Minnesota, Arizona, Florida, Wisconsin, and Iowa) to identify vaccinated and unvaccinated adults between February 27 and July 22, 2021. The unvaccinated cohort was matched on a propensity score derived from age, sex, zip code, race, ethnicity, and previous number of SARS-CoV-2 polymerase chain reaction tests. The final study cohort consisted of 8889 patients in the vaccinated group and 88 898 unvaccinated matched patients. Single dose of the Ad26.COV2.S vaccine. The incidence rate ratio of SARS-CoV-2 infection in the vaccinated vs unvaccinated control cohorts, measured by SARS-CoV-2 polymerase chain reaction testing. The study was composed of 8889 vaccinated patients (4491 men [50.5%]; mean [SD] age, 52.4 [16.9] years) and 88 898 unvaccinated patients (44 748 men [50.3%]; mean [SD] age, 51.7 [16.7] years). The incidence rate ratio of SARS-CoV-2 infection in the vaccinated vs unvaccinated control cohorts was 0.26 (95% CI, 0.20-0.34) (60 of 8889 vaccinated patients vs 2236 of 88 898 unvaccinated individuals), which corresponds to an effectiveness of 73.6% (95% CI, 65.9%-79.9%) and a 3.73-fold reduction in SARS-CoV-2 infections. This study’s findings are consistent with the clinical trial-reported efficacy of Ad26.COV2.S and the first retrospective analysis, suggesting that the vaccine is effective at reducing SARS-CoV-2 infection, even with the spread of variants such as Alpha or Delta that were not present in the original studies, and reaffirm the urgent need to continue mass vaccination efforts globally.</td>
</tr>
<tr class="even">
<td style="text-align: left;">34715931</td>
<td style="text-align: left;">Lessons from Israel’s COVID-19 Green Pass program.</td>
<td style="text-align: left;">Israel journal of health policy research</td>
<td style="text-align: left;">2021 10 29</td>
<td style="text-align: left;">As of the beginning of March 2021, Israeli law requires the presentation of a Green Pass as a precondition for entering certain businesses and public spheres. Entitlement for a Green Pass is granted to Israelis who have been vaccinated with two doses of COVID-19 vaccine, who have recovered from COVID-19, or who are participating in a clinical trial for vaccine development in Israel. The Green Pass is essential for retaining immune individuals’ freedom of movement and for promoting the public interest in reopening the economic, educational, and cultural spheres of activity. Nonetheless, and as the Green Pass imposes restrictions on the movement of individuals who had not been vaccinated or who had not recovered, it is not consonant with solidarity and trust building. Implementing the Green Pass provision while advancing its effectiveness on the one hand, and safeguarding equality, proportionality, and fairness on the other hand may imbue this measure with ethical legitimacy despite involving a potential breach of trust and solidarity. © 2021. The Author(s).</td>
</tr>
<tr class="odd">
<td style="text-align: left;">34713912</td>
<td style="text-align: left;">Vaccine development and technology for SARS-CoV-2: current insights.</td>
<td style="text-align: left;">Journal of medical virology</td>
<td style="text-align: left;">2021 Oct 29</td>
<td style="text-align: left;">SARS-CoV-2 is associated to a severe respiratory disease in China, that rapidly spread across continents. Since the beginning of the pandemic, available data suggested the asymptomatic transmission and patients were treated with specific drugs with efficacy and safety data not always satisfactory. The aim of this review is to describe the vaccines developed by three companies, Pfizer-BioNTech, Moderna and University of Oxford/AstraZeneca, in terms of both technological and pharmaceutical formulation, safety, efficacy and immunogenicity. A critical analysis of phase 1, 2 and 3 clinical trial results available was conducted, comparing the three vaccine candidates, underlining their similarities and differences. All candidates showed consistent efficacy and tolerability; although some differences can be noted, such as their technological formulation, temperature storage, which will be related to logistics and costs. Further studies will be necessary to evaluate long-term effects and to assess the vaccine safety and efficacy in the general population. This article is protected by copyright. All rights reserved. This article is protected by copyright. All rights reserved.</td>
</tr>
<tr class="even">
<td style="text-align: left;">34711598</td>
<td style="text-align: left;">BCG vaccination to reduce the impact of COVID-19 in healthcare workers: Protocol for a randomised controlled trial (BRACE trial).</td>
<td style="text-align: left;">BMJ open</td>
<td style="text-align: left;">2021 10 28</td>
<td style="text-align: left;">BCG vaccination modulates immune responses to unrelated pathogens. This off-target effect could reduce the impact of emerging pathogens. As a readily available, inexpensive intervention that has a well-established safety profile, BCG is a good candidate for protecting healthcare workers (HCWs) and other vulnerable groups against COVID-19. This international multicentre phase III randomised controlled trial aims to determine if BCG vaccination reduces the incidence of symptomatic and severe COVID-19 at 6 months (co-primary outcomes) compared with no BCG vaccination. We plan to randomise 10 078 HCWs from Australia, The Netherlands, Spain, the UK and Brazil in a 1:1 ratio to BCG vaccination or no BCG (control group). The participants will be followed for 1 year with questionnaires and collection of blood samples. For any episode of illness, clinical details will be collected daily, and the participant will be tested for SARS-CoV-2 infection. The secondary objectives are to determine if BCG vaccination reduces the rate, incidence, and severity of any febrile or respiratory illness (including SARS-CoV-2), as well as work absenteeism. The safety of BCG vaccination in HCWs will also be evaluated. Immunological analyses will assess changes in the immune system following vaccination, and identify factors associated with susceptibility to or protection against SARS-CoV-2 and other infections. Ethical and governance approval will be obtained from participating sites. Results will be published in peer-reviewed open-access journals. The final cleaned and locked database will be deposited in a data sharing repository archiving system. ClinicalTrials.gov NCT04327206. © Author(s) (or their employer(s)) 2021. Re-use permitted under CC BY. Published by BMJ.</td>
</tr>
<tr class="odd">
<td style="text-align: left;">34704204</td>
<td style="text-align: left;">COVID-19 Testing and Vaccine Acceptability Among Homeless-Experienced Adults: Qualitative Data from Two Samples.</td>
<td style="text-align: left;">Journal of general internal medicine</td>
<td style="text-align: left;">2021 Oct 26</td>
<td style="text-align: left;">Homeless-experienced populations are at increased risk of exposure to SARS-CoV-2 due to their living environments and face an increased risk of severe COVID-19 disease due to underlying health conditions. Little is known about COVID-19 testing and vaccination acceptability among homeless-experienced populations. To understand the facilitators and barriers to COVID-19 testing and vaccine acceptability among homeless-experienced adults. We conducted in-depth interviews with participants from July to October 2020. We purposively recruited participants from (1) a longitudinal cohort of homeless-experienced older adults in Oakland, CA (n=37) and (2) a convenience sample of people (n=57) during a mobile outreach COVID-19 testing event in San Francisco. Adults with current or past experience of homelessness. We asked participants about their experiences with and attitudes towards COVID-19 testing and their perceptions of COVID-19 vaccinations. We used participant observation techniques to document the interactions between testing teams and those approached for testing. We audio-recorded, transcribed, and content analyzed all interviews and identified major themes and subthemes. Participants found incentivized COVID-19 testing administered in unsheltered settings and supported by community health outreach workers (CHOWs) to be acceptable. The majority of participants expressed a positive inclination toward vaccine acceptability, citing a desire to return to routine life and civic responsibility. Those who expressed hesitancy cited a desire to see trial data, concerns that vaccines included infectious materials, and mistrust of the government. Participants expressed positive evaluations of the incentivized, mobile COVID-19 testing supported by CHOWs in unsheltered settings. The majority of participants expressed a positive inclination toward vaccination. Vaccine hesitancy concerns must be addressed when designing vaccine delivery strategies that overcome access challenges. Based on the successful implementation of COVID-19 testing, we recommend mobile delivery of vaccines using trusted CHOWs to address concerns and facilitate wider access to and uptake of the COVID vaccine. © 2021. Society of General Internal Medicine.</td>
</tr>
<tr class="even">
<td style="text-align: left;">34703690</td>
<td style="text-align: left;">A Rare Variant of Guillain-Barre Syndrome Following Ad26.COV2.S Vaccination.</td>
<td style="text-align: left;">Cureus</td>
<td style="text-align: left;">2021 Sep</td>
<td style="text-align: left;">Efforts to combat the global pandemic caused by severe acute respiratory syndrome coronavirus 2 (SARS-CoV-2) range from adequate diagnostic testing and contract tracing to vaccination for the prevention of coronavirus disease 2019 (COVID-19). In the United States alone, three vaccinations have been authorized for emergency use (EUA) or approved to prevent COVID-19. The Ad26.COV2.S vaccine by Johnson and Johnson (New Brunswick, New Jersey) is the only adenovirus-based vaccine and deemed relatively effective and safe by the US Food and Drug Administration (FDA) following its clinical trial. Since its introduction, the US FDA has placed a warning on the vaccine adverse event reporting system (VAERS) after more than 100 cases of Guillain-Barre Syndrome (GBS) were reported. Herein, we outline the hospital course of a generally healthy 49-year-old female who experienced an axonal form of GBS nine days after receiving the Ad26.COV2.S vaccine. Copyright © 2021, Morehouse et al.</td>
</tr>
<tr class="odd">
<td style="text-align: left;">34702753</td>
<td style="text-align: left;">Humoral immunogenicity of the seasonal influenza vaccine before and after CAR-T-cell therapy: a prospective observational study.</td>
<td style="text-align: left;">Journal for immunotherapy of cancer</td>
<td style="text-align: left;">2021 10</td>
<td style="text-align: left;">Recipients of chimeric antigen receptor-modified T (CAR-T) cell therapies for B cell malignancies have profound and prolonged immunodeficiencies and are at risk for serious infections, including respiratory virus infections. Vaccination may be important for infection prevention, but there are limited data on vaccine immunogenicity in this population. We conducted a prospective observational study of the humoral immunogenicity of commercially available 2019-2020 inactivated influenza vaccines in adults immediately prior to or while in durable remission after CD19-, CD20-, or B cell maturation antigen-targeted CAR-T-cell therapy, as well as controls. We tested for antibodies to all four vaccine strains using neutralization and hemagglutination inhibition (HAI) assays. Antibody responses were defined as at least fourfold titer increases from baseline. Seroprotection was defined as a HAI titer ≥40. Enrolled CAR-T-cell recipients were vaccinated 14-29 days prior to (n=5) or 13-57 months following therapy (n=13), and the majority had hypogammaglobulinemia and cellular immunodeficiencies prevaccination. Eight non-immunocompromised adults served as controls. Antibody responses to ≥1 vaccine strain occurred in 2 (40%) individuals before CAR-T-cell therapy and in 4 (31%) individuals vaccinated after CAR-T-cell therapy. An additional 1 (20%) and 6 (46%) individuals had at least twofold increases, respectively. One individual vaccinated prior to CAR-T-cell therapy maintained a response for &gt;3 months following therapy. Across all tested vaccine strains, seroprotection was less frequent in CAR-T-cell recipients than in controls. There was evidence of immunogenicity even among individuals with low immunoglobulin, CD19+ B cell, and CD4+ T-cell counts. These data support consideration for vaccination before and after CAR-T-cell therapy for influenza and other relevant pathogens such as SARS-CoV-2, irrespective of hypogammaglobulinemia or B cell aplasia. However, relatively impaired humoral vaccine immunogenicity indicates the need for additional infection-prevention strategies. Larger studies are needed to refine our understanding of potential correlates of vaccine immunogenicity, and durability of immune responses, in CAR-T-cell therapy recipients. © Author(s) (or their employer(s)) 2021. Re-use permitted under CC BY-NC. No commercial re-use. See rights and permissions. Published by BMJ.</td>
</tr>
<tr class="even">
<td style="text-align: left;">34698827</td>
<td style="text-align: left;">Measuring vaccine efficacy against infection and disease in clinical trials: sources and magnitude of bias in COVID-19 vaccine efficacy estimates.</td>
<td style="text-align: left;">Clinical infectious diseases : an official publication of the Infectious Diseases Society of America</td>
<td style="text-align: left;">2021 Oct 26</td>
<td style="text-align: left;">Phase III trials have estimated COVID-19 vaccine efficacy (VE) against symptomatic and asymptomatic infection. We explore the direction and magnitude of potential biases in these estimates and their implications for vaccine protection against infection and against disease in breakthrough infections. We developed a mathematical model that accounts for natural and vaccine-induced immunity, changes in serostatus and imperfect sensitivity and specificity of tests for infection and antibodies. We estimated expected biases in VE against symptomatic, asymptomatic and any SARS͏CoV2 infections and against disease following infection for a range of vaccine characteristics and measurement approaches, and the likely overall biases for published trial results that included asymptomatic infections. VE against asymptomatic infection measured by PCR or serology is expected to be low or negative for vaccines that prevent disease but not infection. VE against any infection is overestimated when asymptomatic infections are less likely to be detected than symptomatic infections and the vaccine protects against symptom development. A competing bias towards underestimation arises for estimates based on tests with imperfect specificity, especially when testing is performed frequently. Our model indicates considerable uncertainty in Oxford-AstraZeneca ChAdOx1 and Janssen Ad26.COV2.S VE against any infection, with slightly higher than published, bias-adjusted values of 59.0% (95% uncertainty interval [UI] 38.4 to 77.1) and 70.9% (95% UI 49.8 to 80.7) respectively. Multiple biases are likely to influence COVID-19 VE estimates, potentially explaining the observed difference between ChAdOx1 and Ad26.COV2.S vaccines. These biases should be considered when interpreting both efficacy and effectiveness study results. © The Author(s) 2021. Published by Oxford University Press for the Infectious Diseases Society of America.</td>
</tr>
<tr class="odd">
<td style="text-align: left;">34697214</td>
<td style="text-align: left;">Author Response: Guillain-Barré Syndrome in the Placebo and Active Arms of a COVID-19 Vaccine Clinical Trial.</td>
<td style="text-align: left;">Neurology</td>
<td style="text-align: left;">2021 10 26</td>
<td style="text-align: left;">NA</td>
</tr>
<tr class="even">
<td style="text-align: left;">34697213</td>
<td style="text-align: left;">Reader Response: Guillain-Barré Syndrome in the Placebo and Active Arms of a COVID-19 Vaccine Clinical Trial.</td>
<td style="text-align: left;">Neurology</td>
<td style="text-align: left;">2021 10 26</td>
<td style="text-align: left;">NA</td>
</tr>
<tr class="odd">
<td style="text-align: left;">34696316</td>
<td style="text-align: left;">Characterization of Individuals Interested in Participating in a Phase I SARS-CoV-2 Vaccine Trial.</td>
<td style="text-align: left;">Vaccines</td>
<td style="text-align: left;">2021 Oct 19</td>
<td style="text-align: left;">The development of an effective vaccine against SARS-CoV-2 marks one of the highest priorities during the ongoing pandemic. However, recruitment of participants for clinical trials can be challenging, and recruitment failure is among the most common reasons for discontinuation in clinical trials. From 20 May 2020, public announcements about a planned phase I trial of the vaccine candidate MVA-SARS-2-S against SARS-CoV-2 began, and interested individuals started contacting the study team via designated e-mail. All emails received from these individuals between 20 May 2020-30 September 2020 were assessed. Of the 2541 interested volunteers, 62% contacted the study team within three days after the first media announcement. The average age was 61 years (range 16-100), 48% of volunteers were female and 52% male. A total of 274, 186, and 53 individuals, respectively, reported medical pre-conditions, were health-care workers, or had frequent inter-person contacts. In conclusion, we report a high number of volunteers, with a considerable percentage stating factors for an elevated risk to acquire COVID-19 or develop severe disease. Factors such as media coverage and the perception of a disease as an acute threat may influence the individual’s choice to volunteer for a vaccine trial. Our data provide first important insights to better understand reasons to participate in such trials to facilitate trial implementation and recruitment.</td>
</tr>
<tr class="even">
<td style="text-align: left;">34696233</td>
<td style="text-align: left;">Antibody Persistence 6 Months Post-Vaccination with BNT162b2 among Health Care Workers.</td>
<td style="text-align: left;">Vaccines</td>
<td style="text-align: left;">2021 Oct 03</td>
<td style="text-align: left;">We present immunogenicity data 6 months after the first dose of BNT162b2 in correlation with age, gender, BMI, comorbidities and previous SARS-CoV-2 infection. An immunogenicity evaluation was carried out among health care workers (HCW) vaccinated at the Istituti Fisioterapici Ospitalieri (IFO). All HCW were asked to be vaccine by the national vaccine campaign at the beginning of 2021. Serum samples were collected on day 1 just prior to the first dose of the vaccine and on day 21 just prior to the second vaccination dose. Thereafter sera samples were collected 28, 49, 84 and 168 days after the first dose of BNT162b2. Quantitative measurement of IgG antibodies against S1/S2 antigens of SARS-CoV-2 was performed with a commercial chemiluminescent immunoassay. Two hundred seventy-four HWCs were analyzed, 175 women (63.9%) and 99 men (36.1%). The maximum antibody geometric mean concentration (AbGMC) was reached at T2 (299.89 AU/mL; 95% CI: 263.53-339.52) with a significant increase compared to baseline (p &lt; 0.0001). Thereafter, a progressive decrease was observed. At T5, a median decrease of 59.6% in COVID-19 negative, and of 67.8% in COVID-19 positive individuals were identified with respect to the highest antibody response. At T1, age and previous COVID-19 were associated with differences in antibody response, while at T2 and T3 differences in immune response were associated with age, gender and previous COVID-19. At T4 and T5, only COVID-19 positive participants demonstrated a greater antibody response, whereas no other variables seemed to influence antibody levels. Overall our study clearly shows antibody persistence at 6 months, albeit with a certain decline. Thus, the use of this vaccine in addressing the COVID-19 pandemic is supported by our results that in turn open debate about the need for further boosts.</td>
</tr>
<tr class="odd">
<td style="text-align: left;">34696198</td>
<td style="text-align: left;">A Retrospective Cross-Sectional Study Assessing Self-Reported Adverse Events following Immunization (AEFI) of the COVID-19 Vaccine in Bangladesh.</td>
<td style="text-align: left;">Vaccines</td>
<td style="text-align: left;">2021 Sep 28</td>
<td style="text-align: left;">The Oxford-AstraZeneca vaccine (Covishield) was the first to be introduced in Bangladesh to fight the ongoing global COVID-19 pandemic. As this vaccine had shown some side-effects in its clinical trial, we aimed to conduct a study assessing short-term adverse events following immunization (AEFIs) in Bangladesh. A cross-sectional study was conducted on social and electronic media platforms by delivering an online questionnaire among people who had taken at least one dose of the COVID-19 vaccine. The collected data were then analysed to evaluate various parameters related to the AEFIs of the respondents. A total of 626 responses were collected. Of these, 623 were selected based on complete answers and used for the analysis. Most of the respondents were between 30-60 years of age, and 40.4% were female. We found that a total of 8.5% of the total respondents had been infected with the SARS-CoV-2 virus. Our survey revealed that out of 623 volunteers, 317 reported various side-effects after taking the vaccine, which is about 50.88% of the total participants. The majority of participants (37.07%, 231/623) reported swelling and pain at the injection site and fever (25.84%, 162/623); these were some of the common localized and generalized symptoms after the COVID-19 vaccine administration. The side-effects reported after receiving the Oxford-AstraZeneca vaccine (Covishield) are similar to those reported in clinical trials, demonstrating that the vaccines have a safe therapeutic window. Moreover, further research is needed to determine the efficacy of existing vaccines in preventing SARS-CoV-2 infections or after-infection hospitalization.</td>
</tr>
<tr class="even">
<td style="text-align: left;">34691078</td>
<td style="text-align: left;">Functional Effects of Receptor-Binding Domain Mutations of SARS-CoV-2 B.1.351 and P.1 Variants.</td>
<td style="text-align: left;">Frontiers in immunology</td>
<td style="text-align: left;">2021</td>
<td style="text-align: left;">The recent identification and rise to dominance of the P.1 and B.1.351 SARS-CoV-2 variants have brought international concern because they may confer fitness advantages. The same three positions in the receptor-binding domain (RBD) are affected in both variants, but where the 417 substitution differs, the E484K/N501Y have co-evolved by convergent evolution. Here we characterize the functional and immune evasive consequences of the P.1 and B.1.351 RBD mutations. E484K and N501Y result in gain-of-function with two different outcomes: The N501Y confers a ten-fold affinity increase towards ACE-2, but a modest antibody evasion potential of plasma from convalescent or vaccinated individuals, whereas the E484K displays a significant antibody evasion capacity without a major impact on affinity. On the other hand, the two different 417 substitutions severely impair the RBD/ACE-2 affinity, but in the combined P.1 and B.1.351 RBD variants, this effect is partly counterbalanced by the effect of the E484K and N501Y. Our results suggest that the combination of these three mutations is a two-step forward and one step back in terms of viral fitness. Copyright © 2021 Bayarri-Olmos, Jarlhelt, Johnsen, Hansen, Helgstrand, Rose Bjelke, Matthiesen, Nielsen, Iversen, Ostrowski, Bundgaard, Frikke-Schmidt, Garred and Skjoedt.</td>
</tr>
<tr class="odd">
<td style="text-align: left;">34691071</td>
<td style="text-align: left;">Heterologous Ad26.COV2.S Prime and mRNA-Based Boost COVID-19 Vaccination Regimens: The SWITCH Trial Protocol.</td>
<td style="text-align: left;">Frontiers in immunology</td>
<td style="text-align: left;">2021</td>
<td style="text-align: left;">NA</td>
</tr>
<tr class="even">
<td style="text-align: left;">34690266</td>
<td style="text-align: left;">Acute Corneal Transplant Rejection After Severe Acute Respiratory Syndrome Coronavirus 2 mRNA-1273 Vaccination.</td>
<td style="text-align: left;">Cornea</td>
<td style="text-align: left;">2021 Oct 23</td>
<td style="text-align: left;">The purpose of this article was to report a case of full-thickness corneal transplant rejection 3 days after immunization with the severe acute respiratory syndrome coronavirus 2 (SARS-CoV-2) Moderna mRNA-1273 vaccine. Case Report. A 51-year-old man with a history of keratoconus and penetrating keratoplasty underwent repeat penetrating keratoplasty for graft failure. The patient had an uncomplicated intraoperative and postoperative course with improved vision and a healthy graft. The patient received the SARS-CoV-2 Moderna vaccine on postoperative week 3, and within 3 days, the patient began developing eye pain, photophobia, and blurred vision. The patient was found to have graft rejection with corneal edema and endothelial keratic precipitates. The rejection did not improve despite a trial of increased topical steroids and ultimately evolved into graft failure. To the best of our knowledge, this case of full-thickness graft rejection after the Moderna SARS-CoV-2 mRNA vaccination is the first to be reported worldwide. The temporal relationship between vaccination and subsequent rejection is highly suggestive of causation due to the short interval (3 days) between vaccination and rejection and the lack of other inciting factors in an otherwise healthy graft. Patients with corneal transplants who plan to take the COVID-19 vaccinations should be counseled on symptoms and closely monitored, and an individualized plan should be made in discussion with the ophthalmologist. Copyright © 2021 Wolters Kluwer Health, Inc. All rights reserved.</td>
</tr>
</tbody>
</table>

Twenty papers about SARS-CoV-2 Trial Vaccines

### Text Mining

To tokenize and count words from the dataset provided for this
assignment:

    # Read in data from provided repository
    gitData <- read.csv("pubmed.csv")

    # With stop words
    gitData %>%
      unnest_tokens(output = word, input = abstract) %>%
      count(word, sort = TRUE) %>%
      top_n(20) %>%
      ggplot(aes(x = n, y = fct_reorder(word,n))) +
      geom_col(color = "#1FA971", fill = "#6ACBA4") +
      labs(x = "Occurrences", y = "Word", title = "Top 20 Common Tokens (with stop words)")

    ## Selecting by n

![](AbercrombieJaxon_Assignment3_PM566_files/figure-markdown_strict/token-counts-1.png)

    # Without stop words
    gitData %>%
      unnest_tokens(output = word, input = abstract) %>%
      count(word, sort = TRUE) %>%
      anti_join(stop_words, by = "word") %>%
      filter(!grepl(pattern = "^[0-9]+$", x = word)) %>%
      top_n(20) %>%
      ggplot(aes(x = n, y = fct_reorder(word,n))) +
      geom_col(color = "#247EA2", fill = "#57ABCD") +
      labs(x = "Occurrences", y = "Word", title = "Top 20 Common Tokens (without stop words)")

    ## Selecting by n

![](AbercrombieJaxon_Assignment3_PM566_files/figure-markdown_strict/token-counts-2.png)

    gitData %>%
      unnest_tokens(output = word, input = abstract) %>%
      anti_join(stop_words, by = "word") %>%
      group_by(term) %>%
      count(word, sort = TRUE) %>%
      top_n(5) %>%
      knitr::kable(caption = "Top 5 Common Tokens for Each Term")

    ## Selecting by n

<table>
<caption>Top 5 Common Tokens for Each Term</caption>
<thead>
<tr class="header">
<th style="text-align: left;">term</th>
<th style="text-align: left;">word</th>
<th style="text-align: right;">n</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;">covid</td>
<td style="text-align: left;">covid</td>
<td style="text-align: right;">7275</td>
</tr>
<tr class="even">
<td style="text-align: left;">covid</td>
<td style="text-align: left;">19</td>
<td style="text-align: right;">7035</td>
</tr>
<tr class="odd">
<td style="text-align: left;">prostate cancer</td>
<td style="text-align: left;">cancer</td>
<td style="text-align: right;">3840</td>
</tr>
<tr class="even">
<td style="text-align: left;">prostate cancer</td>
<td style="text-align: left;">prostate</td>
<td style="text-align: right;">3832</td>
</tr>
<tr class="odd">
<td style="text-align: left;">covid</td>
<td style="text-align: left;">patients</td>
<td style="text-align: right;">2293</td>
</tr>
<tr class="even">
<td style="text-align: left;">preeclampsia</td>
<td style="text-align: left;">pre</td>
<td style="text-align: right;">2038</td>
</tr>
<tr class="odd">
<td style="text-align: left;">preeclampsia</td>
<td style="text-align: left;">eclampsia</td>
<td style="text-align: right;">2005</td>
</tr>
<tr class="even">
<td style="text-align: left;">preeclampsia</td>
<td style="text-align: left;">preeclampsia</td>
<td style="text-align: right;">1863</td>
</tr>
<tr class="odd">
<td style="text-align: left;">preeclampsia</td>
<td style="text-align: left;">women</td>
<td style="text-align: right;">1196</td>
</tr>
<tr class="even">
<td style="text-align: left;">preeclampsia</td>
<td style="text-align: left;">pregnancy</td>
<td style="text-align: right;">969</td>
</tr>
<tr class="odd">
<td style="text-align: left;">covid</td>
<td style="text-align: left;">disease</td>
<td style="text-align: right;">943</td>
</tr>
<tr class="even">
<td style="text-align: left;">prostate cancer</td>
<td style="text-align: left;">patients</td>
<td style="text-align: right;">934</td>
</tr>
<tr class="odd">
<td style="text-align: left;">prostate cancer</td>
<td style="text-align: left;">treatment</td>
<td style="text-align: right;">926</td>
</tr>
<tr class="even">
<td style="text-align: left;">cystic fibrosis</td>
<td style="text-align: left;">fibrosis</td>
<td style="text-align: right;">867</td>
</tr>
<tr class="odd">
<td style="text-align: left;">cystic fibrosis</td>
<td style="text-align: left;">cystic</td>
<td style="text-align: right;">862</td>
</tr>
<tr class="even">
<td style="text-align: left;">covid</td>
<td style="text-align: left;">pandemic</td>
<td style="text-align: right;">800</td>
</tr>
<tr class="odd">
<td style="text-align: left;">prostate cancer</td>
<td style="text-align: left;">disease</td>
<td style="text-align: right;">652</td>
</tr>
<tr class="even">
<td style="text-align: left;">cystic fibrosis</td>
<td style="text-align: left;">cf</td>
<td style="text-align: right;">625</td>
</tr>
<tr class="odd">
<td style="text-align: left;">cystic fibrosis</td>
<td style="text-align: left;">patients</td>
<td style="text-align: right;">586</td>
</tr>
<tr class="even">
<td style="text-align: left;">meningitis</td>
<td style="text-align: left;">patients</td>
<td style="text-align: right;">446</td>
</tr>
<tr class="odd">
<td style="text-align: left;">meningitis</td>
<td style="text-align: left;">meningitis</td>
<td style="text-align: right;">429</td>
</tr>
<tr class="even">
<td style="text-align: left;">cystic fibrosis</td>
<td style="text-align: left;">disease</td>
<td style="text-align: right;">400</td>
</tr>
<tr class="odd">
<td style="text-align: left;">meningitis</td>
<td style="text-align: left;">meningeal</td>
<td style="text-align: right;">219</td>
</tr>
<tr class="even">
<td style="text-align: left;">meningitis</td>
<td style="text-align: left;">csf</td>
<td style="text-align: right;">206</td>
</tr>
<tr class="odd">
<td style="text-align: left;">meningitis</td>
<td style="text-align: left;">clinical</td>
<td style="text-align: right;">187</td>
</tr>
</tbody>
</table>

Top 5 Common Tokens for Each Term

When tokenizing the abstracts without removing stopwords, it is clear
that stop words like “the” and “of” are most abundant. The first
noticeable words that are not stop words are “covid” and “19,” which
would be expected given the context of the dataset. Upon removing stop
words, it is evident that the list of top 20 counted words changes and
is more suited to give us better details about the dataset. After
removing stop words, the top 5 common tokens were “covid,” “patients,”
“cancer,” “prostate,” and “disease.”

By search term, the top 5 words are:

-   “covid” - covid (7275), 19 (7035), patients (2293), disease (943),
    pandemic (800)

-   “prostate cancer” - cancer (3840), prostate (3832), patients (934),
    treatment (926), disease (652)

-   “preeclampsia” - pre (2038), eclampsia (2005), preeclampsia (1863),
    women (1196), pregnancy (969)

-   “cystic fibrosis” - fibrosis (867), cystic (862), cf (625), patients
    (586), disease (400)

-   “meningitis” - patients (446), meningitis (429), meningeal (219),
    csf (206), clinical (187)

Then, abstracts were tokenized into bigrams, and stop words were removed
to avoid bigrams containining them too. A visual containing columns for
each of the ten most common bigrams was created.

    gitData %>%
      unnest_ngrams(output = bigram, input = abstract, n = 2) %>%
      separate(bigram, c("word1", "word2"), sep = " ") %>% 
      filter(!word1 %in% stop_words$word) %>%
      filter(!word2 %in% stop_words$word) %>% 
      unite(bigram, word1, word2, sep = " ") %>% 
      count(bigram, sort = TRUE) %>%
      top_n(10) %>%
      ggplot(aes(x = n, y = fct_reorder(bigram,n))) +
      geom_col(color = "#8A2893", fill = "#C470CB") +
      labs(x = "Occurrences", y = "Bigram", title = "Top 10 Common Bigrams")

    ## Selecting by n

![](AbercrombieJaxon_Assignment3_PM566_files/figure-markdown_strict/token-bigrams-1.png)

Lastly, for each word-search term combination, TF-IDF values were
calculated.

    # 
    gitData %>%
      unnest_tokens(output = word, input = abstract) %>%
      count(word, term) %>%
      bind_tf_idf(word, term, n) %>%
      arrange(desc(tf_idf)) %>% 
      group_by(term) %>%
      top_n(5,tf_idf) %>%
      arrange(desc(term)) %>%
      knitr::kable(caption = "Top 5 Words for Each Search Term by TF-IDF Values, sorted by Term")

<table>
<caption>Top 5 Words for Each Search Term by TF-IDF Values, sorted by Term</caption>
<thead>
<tr class="header">
<th style="text-align: left;">word</th>
<th style="text-align: left;">term</th>
<th style="text-align: right;">n</th>
<th style="text-align: right;">tf</th>
<th style="text-align: right;">idf</th>
<th style="text-align: right;">tf_idf</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;">prostate</td>
<td style="text-align: left;">prostate cancer</td>
<td style="text-align: right;">3832</td>
<td style="text-align: right;">0.0311890</td>
<td style="text-align: right;">1.6094379</td>
<td style="text-align: right;">0.0501967</td>
</tr>
<tr class="even">
<td style="text-align: left;">androgen</td>
<td style="text-align: left;">prostate cancer</td>
<td style="text-align: right;">305</td>
<td style="text-align: right;">0.0024824</td>
<td style="text-align: right;">1.6094379</td>
<td style="text-align: right;">0.0039953</td>
</tr>
<tr class="odd">
<td style="text-align: left;">psa</td>
<td style="text-align: left;">prostate cancer</td>
<td style="text-align: right;">282</td>
<td style="text-align: right;">0.0022952</td>
<td style="text-align: right;">1.6094379</td>
<td style="text-align: right;">0.0036940</td>
</tr>
<tr class="even">
<td style="text-align: left;">prostatectomy</td>
<td style="text-align: left;">prostate cancer</td>
<td style="text-align: right;">215</td>
<td style="text-align: right;">0.0017499</td>
<td style="text-align: right;">1.6094379</td>
<td style="text-align: right;">0.0028164</td>
</tr>
<tr class="odd">
<td style="text-align: left;">castration</td>
<td style="text-align: left;">prostate cancer</td>
<td style="text-align: right;">148</td>
<td style="text-align: right;">0.0012046</td>
<td style="text-align: right;">1.6094379</td>
<td style="text-align: right;">0.0019387</td>
</tr>
<tr class="even">
<td style="text-align: left;">eclampsia</td>
<td style="text-align: left;">preeclampsia</td>
<td style="text-align: right;">2005</td>
<td style="text-align: right;">0.0142784</td>
<td style="text-align: right;">1.6094379</td>
<td style="text-align: right;">0.0229802</td>
</tr>
<tr class="odd">
<td style="text-align: left;">preeclampsia</td>
<td style="text-align: left;">preeclampsia</td>
<td style="text-align: right;">1863</td>
<td style="text-align: right;">0.0132672</td>
<td style="text-align: right;">1.6094379</td>
<td style="text-align: right;">0.0213527</td>
</tr>
<tr class="even">
<td style="text-align: left;">pregnancy</td>
<td style="text-align: left;">preeclampsia</td>
<td style="text-align: right;">969</td>
<td style="text-align: right;">0.0069006</td>
<td style="text-align: right;">0.5108256</td>
<td style="text-align: right;">0.0035250</td>
</tr>
<tr class="odd">
<td style="text-align: left;">maternal</td>
<td style="text-align: left;">preeclampsia</td>
<td style="text-align: right;">797</td>
<td style="text-align: right;">0.0056757</td>
<td style="text-align: right;">0.5108256</td>
<td style="text-align: right;">0.0028993</td>
</tr>
<tr class="even">
<td style="text-align: left;">gestational</td>
<td style="text-align: left;">preeclampsia</td>
<td style="text-align: right;">191</td>
<td style="text-align: right;">0.0013602</td>
<td style="text-align: right;">1.6094379</td>
<td style="text-align: right;">0.0021891</td>
</tr>
<tr class="odd">
<td style="text-align: left;">meningitis</td>
<td style="text-align: left;">meningitis</td>
<td style="text-align: right;">429</td>
<td style="text-align: right;">0.0091942</td>
<td style="text-align: right;">1.6094379</td>
<td style="text-align: right;">0.0147974</td>
</tr>
<tr class="even">
<td style="text-align: left;">meningeal</td>
<td style="text-align: left;">meningitis</td>
<td style="text-align: right;">219</td>
<td style="text-align: right;">0.0046935</td>
<td style="text-align: right;">1.6094379</td>
<td style="text-align: right;">0.0075539</td>
</tr>
<tr class="odd">
<td style="text-align: left;">pachymeningitis</td>
<td style="text-align: left;">meningitis</td>
<td style="text-align: right;">149</td>
<td style="text-align: right;">0.0031933</td>
<td style="text-align: right;">1.6094379</td>
<td style="text-align: right;">0.0051394</td>
</tr>
<tr class="even">
<td style="text-align: left;">csf</td>
<td style="text-align: left;">meningitis</td>
<td style="text-align: right;">206</td>
<td style="text-align: right;">0.0044149</td>
<td style="text-align: right;">0.9162907</td>
<td style="text-align: right;">0.0040453</td>
</tr>
<tr class="odd">
<td style="text-align: left;">meninges</td>
<td style="text-align: left;">meningitis</td>
<td style="text-align: right;">106</td>
<td style="text-align: right;">0.0022718</td>
<td style="text-align: right;">1.6094379</td>
<td style="text-align: right;">0.0036562</td>
</tr>
<tr class="even">
<td style="text-align: left;">cf</td>
<td style="text-align: left;">cystic fibrosis</td>
<td style="text-align: right;">625</td>
<td style="text-align: right;">0.0127188</td>
<td style="text-align: right;">0.9162907</td>
<td style="text-align: right;">0.0116541</td>
</tr>
<tr class="odd">
<td style="text-align: left;">fibrosis</td>
<td style="text-align: left;">cystic fibrosis</td>
<td style="text-align: right;">867</td>
<td style="text-align: right;">0.0176435</td>
<td style="text-align: right;">0.5108256</td>
<td style="text-align: right;">0.0090127</td>
</tr>
<tr class="even">
<td style="text-align: left;">cystic</td>
<td style="text-align: left;">cystic fibrosis</td>
<td style="text-align: right;">862</td>
<td style="text-align: right;">0.0175417</td>
<td style="text-align: right;">0.5108256</td>
<td style="text-align: right;">0.0089608</td>
</tr>
<tr class="odd">
<td style="text-align: left;">cftr</td>
<td style="text-align: left;">cystic fibrosis</td>
<td style="text-align: right;">86</td>
<td style="text-align: right;">0.0017501</td>
<td style="text-align: right;">1.6094379</td>
<td style="text-align: right;">0.0028167</td>
</tr>
<tr class="even">
<td style="text-align: left;">sweat</td>
<td style="text-align: left;">cystic fibrosis</td>
<td style="text-align: right;">83</td>
<td style="text-align: right;">0.0016891</td>
<td style="text-align: right;">1.6094379</td>
<td style="text-align: right;">0.0027184</td>
</tr>
<tr class="odd">
<td style="text-align: left;">covid</td>
<td style="text-align: left;">covid</td>
<td style="text-align: right;">7275</td>
<td style="text-align: right;">0.0371050</td>
<td style="text-align: right;">1.6094379</td>
<td style="text-align: right;">0.0597183</td>
</tr>
<tr class="even">
<td style="text-align: left;">pandemic</td>
<td style="text-align: left;">covid</td>
<td style="text-align: right;">800</td>
<td style="text-align: right;">0.0040803</td>
<td style="text-align: right;">1.6094379</td>
<td style="text-align: right;">0.0065670</td>
</tr>
<tr class="odd">
<td style="text-align: left;">coronavirus</td>
<td style="text-align: left;">covid</td>
<td style="text-align: right;">647</td>
<td style="text-align: right;">0.0032999</td>
<td style="text-align: right;">1.6094379</td>
<td style="text-align: right;">0.0053110</td>
</tr>
<tr class="even">
<td style="text-align: left;">sars</td>
<td style="text-align: left;">covid</td>
<td style="text-align: right;">372</td>
<td style="text-align: right;">0.0018973</td>
<td style="text-align: right;">1.6094379</td>
<td style="text-align: right;">0.0030536</td>
</tr>
<tr class="odd">
<td style="text-align: left;">cov</td>
<td style="text-align: left;">covid</td>
<td style="text-align: right;">334</td>
<td style="text-align: right;">0.0017035</td>
<td style="text-align: right;">1.6094379</td>
<td style="text-align: right;">0.0027417</td>
</tr>
</tbody>
</table>

Top 5 Words for Each Search Term by TF-IDF Values, sorted by Term

Based on the results in the produced table, there are certainly some
different results than the table without considering TF-IDF. For each
term, there were new words like:

-   “prostate cancer” - androgen, psa, prostatectomy, castration

-   “preeclampsia” - maternal, gestational

-   “meningitis” - pachymeningitis, meninges

-   “cystic fibrosis” - cftr, sweat

-   “covid” - sars, coronavirus, cov
