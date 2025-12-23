
```dataview
TABLE 
    salaire_fixe AS "Fixe Mensuel (€)", 
    bonus_mensuel AS "Primes/Mois (€)", 
    teletravail AS "Télétravail",
    etudes AS "Diplôme"
FROM "02_Fiches_Metiers"
WHERE type = "fiche_metier"
SORT salaire_fixe DESC
```


| **Source**    | **Métier** | **Salaire annoncé**      | **Observations**                         |
| ------------- | ---------- | ------------------------ | ---------------------------------------- |
| **ONISEP**    | Actuaire   | 3 300 € brut 4444        | Référence standard 5                     |
| **CIDJ**      | Actuaire   | Variable 6               | Précise le rôle de haut technicien 7     |
| **Entretien** | Actuaire   | 4 250 € brut (51k/an) 88 | Réalité du secteur conseil (Mazars) 9999 |
| **CIDJ**      | Enseignant | 2 200 € à 3 200 € 101010 | Selon le grade (MCF ou PU) 11            |
## Stratégie de recherche
Pour ce projet, j'ai croisé trois types de sources pour garantir la fiabilité des informations :
1. **Sources Institutionnelles** : Utilisation de l'ONISEP et du CIDJ pour les bases du métier (formation, salaire moyen).
2. **Sources Professionnelles** : Consultation de l'Institut des Actuaires pour comprendre l'évolution du métier vers la data.
3. **Enquêtes de terrain** : Entretiens directs pour confronter la théorie à la réalité du quotidien (Mazars et UJM).

## Analyse critique des données
J'ai remarqué des écarts entre les salaires annoncés :
- L'**ONISEP** annonce 3 300 € brut.
- Le **secteur privé (Mazars)** montre qu'en cabinet de conseil, on peut débuter à 4 250 € brut (51k€/an).
Cela prouve que le secteur d'activité (Banque vs Conseil) influence grandement la rémunération

[Le salaire de l'actuaire : guide complet - Superprof](https://www.superprof.fr/blog/actuaire-salaire/)

```r
# Installation des packages nécessaires
install.packages("rvest")
library(rvest)

# URL exemple (Superprof)
url <- "https://www.superprof.fr/blog/actuaire-salaire/"

# Lecture du contenu HTML
page <- read_html(url)

# Extraction des titres ou paragraphes contenant les salaires
# Note : il faut inspecter le site pour trouver les balises CSS précises
salaires_texte <- page %>% 
  html_nodes("p") %>% 
  html_text()

# Affichage des premières lignes extraites
head(salaires_texte)
```


![[Pasted image 20251223025600.png]]


[Enquête Carrière et Rémunération 2024 - Institut des Actuaires](https://www.institutdesactuaires.com/docs/2024140753_enquete-carriere-et-remuneration.pdf)

```r
# Installation du package PDF
install.packages("pdftools")
library(pdftools)

# Lecture du PDF directement depuis l'URL
pdf_url <- "https://www.institutdesactuaires.com/docs/2024140753_enquete-carriere-et-remuneration.pdf"
texte_pdf <- pdf_text(pdf_url)

# Extraction de la page 17 (qui contient les données salariales moyennes)
# Les données montrent un salaire moyen de 92K€
page_17 <- texte_pdf[17]
cat(page_17)
```

![[Pasted image 20251223025839.png]]


[Salaires Actuaire en France - Glassdoor](https://www.glassdoor.fr/Salaires/actuaire-salaire-SRCH_KO0,8.htm)

```r
# Installation et chargement
if(!require(pdftools)) install.packages("pdftools")
library(pdftools)

# Données clés extraites de l'enquête Institut des Actuaires 2024
# Salaire moyen junior (<5 ans) : 56 400€ | Télétravail : 2-3 j/semaine | Variable : 20 000€
donnees_actuariat <- list(
  salaire_annuel_junior = 56400,
  teletravail = "2 à 3 jours/semaine",
  prime_variable = 20000,
  participation = 10000
)

# Fonction de formatage
generer_yaml_complet <- function(d) {
  fixe_mensuel <- round(d$salaire_annuel_junior / 12)
  bonus_mensuel <- round((d$prime_variable + d$participation) / 12)
  
  cat("---\n")
  cat("type: fiche_metier\n")
  cat("metier: Actuaire\n")
  cat("salaire_fixe: ", fixe_mensuel, "\n")
  cat("bonus_mensuel: ", bonus_mensuel, "\n")
  cat("teletravail: ", d$teletravail, "\n")
  cat("logiciels:\n  - Excel (VBA)\n  - Python\n  - R\n  - SAS\n")
  cat("source: Institut des Actuaires 2024\n")
  cat("---\n")
}

generer_yaml_complet(donnees_actuariat)
```


![[Pasted image 20251223031604.png]]