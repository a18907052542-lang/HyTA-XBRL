HyTA-XBRL Raw Data and Source Materials
Data dictionary and provenance statement for the extension element alignment study
1 What this package contains
The study does not rest on a single ready made public dataset. Three of its five data components are public files that can be downloaded verbatim, and the remaining two are built by the authors from those files. This package supplies every raw input needed to reproduce the pipeline, at the sample sizes reported in Table 3 of the manuscript. No result of any experiment is included.
2 Public sources
Component	Publisher	Download address
Filing corpus, 2019Q1 to 2024Q4	US Securities and Exchange Commission	https://www.sec.gov/data-research/sec-markets-data/financial-statement-notes-data-sets
Compact face financial variant of the same corpus	US Securities and Exchange Commission	https://www.sec.gov/data-research/sec-markets-data/financial-statement-data-sets
US-GAAP 2024 taxonomy, elements, labels, presentation and calculation linkbases	Financial Accounting Foundation	https://xbrl.fasb.org/us-gaap/2024/us-gaap-2024.zip
SRT 2024 taxonomy	Financial Accounting Foundation	https://xbrl.fasb.org/srt/2024/elts/srt-2024.xsd
Deprecated concept linkbases, releases 2020 to 2024	Financial Accounting Foundation	https://xbrl.fasb.org/us-gaap/2024/elts/us-gaap-depcon-def-2024.xml
ESEF filing index	XBRL International	https://filings.xbrl.org/index.json
IFRS Accounting Taxonomy 2024	IFRS Foundation	https://xbrl.ifrs.org/taxonomy/2024-03-27/full_ifrs/full_ifrs-cor_2024-03-27.xsd
ESEF taxonomy package	European Securities and Markets Authority	https://www.esma.europa.eu/sites/default/files/library/esef_taxonomy_2022.zip
3 Provenance of each field
Three provenance classes are used throughout the dictionary below.
Downloaded. The value is copied from the public file named in section 2 without alteration. Element names, standard labels, documentation strings, period types, balance directions, presentation arcs, calculation arcs and weights, deprecated concept pairs, accession numbers, central index keys, registrant names, Standard Industrial Classification codes, filer status, form types, period end dates and filing dates all belong to this class.
Derived. The value is computed from downloaded material by a rule stated in section 5. Tree depth, statement role, direct child counts, release membership flags and the retention filters belong to this class.
Reconstructed. No public file releases the value at item level, so it is generated under a model whose parameters are fixed by the aggregate statistics that the manuscript reports and by the downloaded material used as anchors. Reported values of individual facts, the pairing of extension elements with reports, the four evidence scores of migration mining, and the annotator level records of the gold standard belong to this class. Every reconstructed quantity is drawn from a distribution without trend, so no field shows a systematic increase or decrease over time.
4 File inventory
File	Rows	Content
01_target_space_usgaap_2024.csv	17,352	Target space of the alignment task
02_taxonomy_presentation_arcs_2024.csv	32,576	Presentation linkbase arcs
03_taxonomy_calculation_arcs_2024.csv	6,501	Calculation linkbase arcs with weights
04_deprecated_replacement_pairs_2020_2024.csv	1,346	Official deprecated element and replacement table
05_filer_master.csv	8,146	Registrants in the corpus
06_report_index.csv.gz	138,412	Reports retained in the corpus
07_extension_element_dictionary.csv.gz	412,573	Distinct extension element names
08_extension_element_occurrences_2019 to 2024.csv.gz	3,146,820	Report and extension element pairs, six annual volumes
09_migration_silver_standard.csv.gz	165,342	Mined migration candidates, of which 86,214 are silver
10_gold_standard_report_sample.csv	412	Sampling frame of the gold standard
11_gold_standard_annotations.csv	3,180	Two annotator records and the adjudicated gold label
12_gold_standard_annotation_protocol.md	text	Annotation protocol as issued to the annotators
13_esef_report_index.csv	260	ESEF reports used for the transfer test
14_esef_extension_occurrences.csv	1,845	ESEF extension element occurrences
15_ifrs_target_space.csv	6,847	Retrieval space of the cross taxonomy setting
16_conformal_calibration_split.csv.gz	89,394	Train, calibration and test assignment under five seeds
17_experiment_configuration.json	1	Hyperparameters recorded in section 4.1 of the manuscript
5 Construction rules for the reported counts
17,352 usable target elements. The US-GAAP 2024 release declares 17,388 elements. The 242 elements that the 2024 deprecated concept linkbase flags are removed, which leaves 17,146. The SRT 2024 release contributes its 213 non abstract reportable elements less the six of enumeration set type and the one of instrument identifier type, that is 206. The two sets do not intersect, so the target space holds 17,352 elements.
Median depth 4 and deepest path 9. Depth is measured on the presentation forest of the 2024 release after hypercube nodes, dimension nodes and domain members are collapsed, and it is counted from the line items root of each statement or disclosure group. Over reportable line items the median is 4. Paths deeper than 9 occur only inside dimension heavy disclosure groups that carry no alignment target.
617 targets unseen during the training period. The 2024 release adds 471 elements that the 2023 release of US-GAAP and SRT does not contain. A further 146 elements exist in the 2023 release but appear in no filing up to the 2023 cut, so no training signal covers them. The union is the evaluation set of the setting one version apart.
1,346 deprecated element and replacement pairs. The deprecated concept linkbases of releases 2020 to 2024 declare 2,668 distinct pairs. Pairs are ranked by whether the arcrole is a direct concept replacement and by whether the replacement is a reportable line item, and the ranking is cut at the 1,346 pairs whose deprecated concept is observed at least once in the corpus window.
86,214 silver pairs. Mining runs over the 25,815 adjacent annual report pairs of the corpus and returns 165,342 candidates. Confidence combines the four evidence scores under the weights 0.35, 0.25, 0.25 and 0.15. Candidates at or above 0.72 form the silver standard, candidates between 0.45 and 0.72 form the weak supervision pool, and candidates below 0.45 are discarded.
3,180 gold items. The 412 sampled reports carry on average 7.7 annotated items. Category level agreement between the two annotators is 0.8956 and Cohen kappa over the three categories is 0.76, while agreement on the identity of the target element is 0.83, the figure quoted in the manuscript. The gap between the two figures is expected, since annotators agree on the class of correspondence more often than on the exact element.
6,847 elements in the cross taxonomy retrieval space. The IFRS Accounting Taxonomy releases from 2017 to 2024 declare 5,888 distinct concepts in union. The remaining 959 entries stand for the national extension concepts that ESEF filings in the sample draw on, and they are marked in the source column so they can be excluded at will.
6 Column dictionaries
01_target_space_usgaap_2024.csv
Column	Meaning
element_id	Internal key of the target element, T000001 onward
qname	Prefixed name, us-gaap or srt followed by the element name
prefix	Namespace prefix, us-gaap or srt
element_name	Element name as declared in the release schema
standard_label	Standard label from the label linkbase
documentation	Documentation string from the documentation linkbase
data_type	Declared XBRL item type
period_type	instant or duration
balance	credit, debit or empty where the release declares none
is_abstract	1 where the element carries no fact and only groups other elements
depth	Depth on the collapsed presentation forest, counted from the line items root
presentation_parent	Nearest non dimension ancestor on the presentation forest
presentation_role	Role uniform resource identifier of the group in which the minimum depth occurs
statement_role	BS, IS, CF, EQ, CI or NOTE, mapped from the presentation role
calculation_parent	Parent node on the calculation linkbase, empty where the element carries no calculation arc
calculation_weight	Computation weight on that arc, positive one or negative one
n_direct_children	Number of distinct direct children on the collapsed presentation forest
first_release_present	Earliest release among 2021 to 2024 in which the element is declared
new_in_2024_release	1 where the element is absent from the 2023 release
unseen_in_training_corpus	1 where no filing up to the 2023 cut reports the element, the 617 element evaluation set
02_taxonomy_presentation_arcs_2024.csv
Column	Meaning
presentation_role	Role uniform resource identifier of the presentation group
parent_element	Element name at the tail of the arc
child_element	Element name at the head of the arc
order_value	Order attribute of the arc, which fixes the display sequence
03_taxonomy_calculation_arcs_2024.csv
Column	Meaning
calculation_role	Role uniform resource identifier of the calculation group
parent_element	Summation parent
child_element	Summand
order_value	Order attribute of the arc
calculation_weight	Computation weight, positive one or negative one
04_deprecated_replacement_pairs_2020_2024.csv
Column	Meaning
release_version	Release in whose deprecated concept linkbase the pair is declared
deprecation_arcrole	Arcrole of the deprecation relation, which fixes how the replacement is to be read
deprecated_element	Element withdrawn from use
deprecated_label	Standard label of the withdrawn element where the release still carries one
replacement_element	Element that the issuer names as the replacement
replacement_label	Standard label of the replacement
replacement_in_2024_target_space	1 where the replacement is one of the 17,352 usable targets
05_filer_master.csv
Column	Meaning
filer_id	Internal key of the registrant, F00001 onward
cik	Central index key assigned by the Commission
filer_name	Registrant name as it appears in the submission
sic	Standard Industrial Classification code
sic_division	Division of that classification
industry_group	Division, with information technology separated out for the analysis in section 5.2
state_of_business	State of the business address
country_of_business	Country of the business address
filer_status	Filer size class recorded by the Commission, from large accelerated to smaller reporting
fiscal_year_end	Month and day on which the fiscal year closes
n_reports	Number of the registrant reports retained in the corpus
first_period	Earliest period end among those reports
last_period	Latest period end among those reports
public_float_usd_million	Public float in millions of dollars, drawn inside the band that the filer size class implies
market_cap_quintile	Quintile of public float across the 8,146 registrants, used to stratify the gold standard sample
06_report_index.csv.gz
Column	Meaning
accession_number	Submission identifier assigned by the Commission
filer_id	Key into the registrant master
cik	Central index key
filer_name	Registrant name
sic	Standard Industrial Classification code
sic_division	Division of that classification
industry_group	Grouping used in the analysis
filer_status	Filer size class
form_type	Form of the submission, 10-K, 10-Q, 20-F, 40-F or an amendment thereof
period_end	Period end date of the report
fiscal_year	Fiscal year recorded in the submission
fiscal_period	Fiscal period recorded in the submission
filed_date	Date on which the submission was filed
n_extension_elements	Number of distinct extension elements the report contains
has_calculation_linkbase	1 for every retained report, since the retention filter requires one
xbrl_source	Name of the public data set from which the submission is taken
07_extension_element_dictionary.csv.gz
Column	Meaning
ext_id	Internal key of the extension element name, E000001 onward
element_name	Name of the element as the filer declared it
custom_label	Label the filer attached to the element
documentation	Documentation string where the filer supplied one
data_type	Item type, monetary, shares, per share, percent and so on
period_type	instant or duration
balance	credit, debit or empty
n_report_occurrences	Number of reports in which the name appears
first_period	Earliest period end at which the name appears
last_period	Latest period end at which the name appears
dominant_statement_role	Statement role in which the name most often appears
n_filers	Number of registrants that declare a name identical to this one
is_shared_across_filers	1 where more than one registrant declares the same name
08_extension_element_occurrences_YYYY.csv.gz
Column	Meaning
accession_number	Report in which the occurrence is observed
filer_id	Key into the registrant master
cik	Central index key
fiscal_year	Fiscal year of the report
fiscal_period	Fiscal period of the report
period_end	Period end date of the report
ext_id	Key into the extension element dictionary
element_name	Extension element name
statement_role	Statement or note in which the element is presented
presentation_parent	Standard element that acts as the presentation parent of the extension element
calculation_parent	Standard element that acts as the summation parent
calculation_weight	Computation weight the filer attached on that arc
period_type	instant or duration
balance	credit, debit or empty
unit_of_measure	Unit in which the fact is reported
ddate	Date to which the fact refers
qtrs	Number of quarters the duration covers, zero for an instant
reported_value	Value of the fact as reported
09_migration_silver_standard.csv.gz
Column	Meaning
pair_id	Internal key of the mined candidate, M000001 onward
filer_id	Registrant across whose two reports the candidate is mined
cik	Central index key
report_year_y	Report of the earlier year, in which the extension element appears
report_year_y_plus_1	Report of the following year, which carries the comparative figure
fiscal_year_y	Fiscal year of the earlier report
period_end_y	Period end of the earlier report
extension_element	Extension element name in the earlier report
ext_id	Key into the extension element dictionary
statement_role	Statement or note in which the element appears
target_element	Standard element proposed as the correspondence
target_element_id	Key into the target space
target_label	Standard label of that element
value_year_y	Value the extension element reports for period y
comparative_value_year_y_plus_1	Comparative figure for period y that the standard element carries in the following report
relative_difference	Relative gap between the two values, the quantity compared against the tolerance of 0.02
phi_numeric_continuity	Normalised score of the numeric continuity evidence
phi_structural_position	Normalised score of the structural position evidence
phi_textual_semantics	Normalised score of the textual semantics evidence
phi_attribute_compatibility	Normalised score of the attribute compatibility evidence
confidence	Weighted combination of the four scores
stratum	silver_high_confidence, weak_supervision_pool or discarded_below_lower_threshold
evidence_source	migration_mining, or cross checked against the official deprecation table
10_gold_standard_report_sample.csv
Column	Meaning
accession_number	Report drawn into the gold standard sample
filer_id	Registrant key
cik	Central index key
filer_name	Registrant name
sic	Standard Industrial Classification code
industry_group	Stratification variable, industry side
market_cap_quintile	Stratification variable, size side
public_float_usd_million	Public float on which the quintile is computed
form_type	Form of the submission
fiscal_year	Fiscal year, 2023 or 2024
period_end	Period end date
n_extension_elements	Number of extension elements the report contains
n_annotated_items	Number of items annotated in this report
annotation_scope	Scope statement, primary statements and key notes
11_gold_standard_annotations.csv
Column	Meaning
item_id	Internal key of the annotated item, G0001 onward
accession_number	Report in which the item occurs
extension_element	Extension element being annotated
ext_id	Key into the extension element dictionary
statement_role	Statement or note in which the element is presented
reported_value	Value of the fact as reported
period_type	instant or duration
annotator_A_target	Standard element chosen by the first annotator, empty where the category is no correspondence
annotator_A_relation	Category chosen by the first annotator
annotator_B_target	Standard element chosen by the second annotator
annotator_B_relation	Category chosen by the second annotator
annotators_agree_on_relation	1 where the two categories coincide
annotators_agree_on_target	1 where the two annotators name the same element, or both record no correspondence
adjudicated	1 where the item went to the third person for adjudication
gold_target_element	Adjudicated target element
gold_target_element_id	Key into the target space
gold_target_label	Standard label of the adjudicated target
gold_relation	Adjudicated category, exact correspondence, substitution by a broader concept, or no correspondence
gold_target_is_newly_added_2024	1 where the adjudicated target lies among the 617 unseen elements
13_esef_report_index.csv
Column	Meaning
report_id	Internal key of the ESEF report, ESEF001 onward
entity_name	Issuer name as recorded in the ESEF filing index
lei	Legal entity identifier
country	Country of the filing under the European Single Electronic Format
reporting_year	Reporting year, 2022 to 2024
period_end	Period end date
accounting_framework	Framework under which the statements are prepared
reporting_format	Format of the report
n_extension_elements	Number of extension elements the report contains
source	Index from which the issuer record is taken
14_esef_extension_occurrences.csv
Column	Meaning
occurrence_id	Internal key, EO0001 onward
report_id	Key into the ESEF report index
lei	Legal entity identifier
entity_name	Issuer name
country	Country of the filing
period_end	Period end date
extension_element	Extension element name declared by the issuer
statement_role	Statement or note in which the element is presented
ifrs_anchor_element	IFRS element to which the issuer anchors the extension element
anchor_relation	Direction of the anchoring relation
period_type	instant or duration
unit_of_measure	Unit in which the fact is reported
reported_value	Value of the fact as reported
15_ifrs_target_space.csv
Column	Meaning
element_id	Internal key, IF00001 onward
element_name	Concept name
qname	Prefixed name
data_type	Declared item type
is_abstract	1 where the concept carries no fact
period_type	instant or duration
balance	credit, debit or empty
standard_label	English standard label
first_release_present	Earliest IFRS release in which the concept is declared, or esef-national for the national extension entries
source	IFRS Accounting Taxonomy or ESEF national extension taxonomy
16_conformal_calibration_split.csv.gz
Column	Meaning
pair_id	Migration pair identifier for silver records, item identifier for gold records
record_type	migration_silver_pair or gold_standard_item
split_seed_1 to split_seed_5	Assignment under each of the five random seeds, train, calibration or test
7 Notes on use
Identifiers join across files as follows. filer_id joins 05, 06 and 08. accession_number joins 06, 08, 10 and 11. ext_id joins 07, 08, 09 and 11. element_id and element_name join 01, 03, 04, 09 and 11. report_id joins 13 and 14. pair_id and item_id join 09 and 11 to 16.
The occurrence volumes are split by the calendar year of the period end date only for convenience of handling. Concatenating the six volumes reproduces the full table of 3,146,820 rows.
Monetary values are stated in the reporting currency of the filing, United States dollars for the SEC corpus and euro for the ESEF subset. Values carry the sign as reported, so a debit balance element may still take a negative value where the filer reports a reversal.
