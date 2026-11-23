# Financial Transaction & Fraud Analytics

A Tableau portfolio project exploring transaction volumes, records labeled as fraud, and transaction/channel behavior.

This is descriptive analytics. It is not a deployed fraud-prediction service, a real-time alerting system, or proof of financial loss prevented.

## View the workbook and dashboards

[Project resources on Google Drive](https://drive.google.com/drive/folders/1FDkRmjcbCavGXAAUjDMrZmtgLoLhoJbK?usp=sharing)

The linked resources include a Tableau packaged workbook and three dashboard images. The GitHub repository currently contains this README and a license file; the workbook and images are hosted externally, not committed here.

## Dashboard scope

- **Executive overview:** transaction value, transaction counts, fraud-labeled records, and time trends.
- **Fraud monitoring:** fraud rates and amounts across segments, with interval-based comparisons in the workbook.
- **Transaction & channel behavior:** transaction patterns by time, channel, and other available dimensions.

## Measures and interpretation

- **Transaction volume:** the sum of transaction amounts.
- **Fraud exposure:** the sum of amounts for records where `Is_Fraud = 1`. This is not verified net loss, a chargeback total, or a recovery-adjusted figure.
- **Fraud rate:** a ratio based on fraud-labeled records and the workbook's transaction-count measure.
- **Segment comparison:** the inspected Tableau workbook contains Wilson interval calculations with a Z value of 1.96. Its comparison field flags a deviation when the overall baseline falls outside the segment interval, in either direction. This is not a control-chart upper control limit or evidence of a causal risk driver.
- **Trend smoothing:** the workbook includes a moving average over the current mark and six preceding marks. Its time meaning depends on the worksheet's date granularity and table-calculation settings.

The workbook's `Total Transactions` calculation counts non-null customer IDs, while `Segment Sample Size` counts records. Those denominators should be reconciled before interpreting segment comparisons as directly comparable rates.

## Data model

The packaged workbook uses a transaction fact table and related dimensions for date, geography, demographics, device, merchant category, transaction type, and account type. This README describes the supplied Tableau artifact and does not claim that SQL, Python, Power Query, DAX, or a machine-learning application is included in this repository.

## Open and review

1. Open the linked project folder and locate the `.twbx` workbook.
2. Open it with compatible Tableau software. If prompted, review the workbook's packaged data connections.
3. Review dashboard filters and calculation settings before comparing totals or segments.
4. Use the dashboard images when Tableau is unavailable. They show saved states, not a live monitoring feed.

## Known limits

- The supplied source file was observed to contain 200,000 data rows, while the dashboard shows 199,999 transactions. The count definition, missing IDs, filters, and import steps need to be checked before a final total is stated.
- Fraud labels are dataset labels. Their original definition and validation method have not yet been documented here.
- Spikes and segment differences can guide investigation, but do not establish coordinated attacks, botnets, or any other cause.
- The dataset's original source, reuse terms, preparation steps, and any course/tutorial contribution still need attribution. No bank deployment or paid client engagement is claimed.

## License and author

The repository includes an [MIT License](LICENSE). Dataset and external-asset reuse rights must be verified separately.

[Ahmed Badour](https://github.com/Ahmedbadour14)
