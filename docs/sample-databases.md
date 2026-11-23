# Sample databases

Start with the Small sample. Ask both analysis routes **“How many runs did the network schedule on November 2025 service days, including cancellations and runs without observations?”** The published schedule contains **6,770 runs**. A run with no arrival message still counts. Joining all arrival messages before counting can count a corrected run twice.

## Development samples

These three PostgreSQL 18 databases contain synthetic records generated for development. Their expected answers are public so that a developer can diagnose a regression. A table count treats a partitioned fact table as one table. Each row count includes all records in the listed database.

| Tier and database | Scale | What the analyst has to resolve |
|---|---|---|
| Small · Transit operations · `bramble_dev_transit` | 4 tables · 286,459 rows | Reconcile scheduled runs, dispatch decisions and corrected arrivals across service-day boundaries. |
| Medium · Parcel logistics · `bramble_dev_logistics` | 9 tables · 1,896,667 rows | Match parcel manifests to carrier invoices while handling late matches, repeated lines and revisions. |
| Enterprise · Electricity billing · `bramble_dev_energy` | 17 tables · 22,858,045 rows | Reconcile metered usage, bills and receivable postings across ownership changes. Equal and opposite posting errors can disappear in a monthly total. |

An independent review rated analytical scale and complexity **8/10 for Small, 8.5/10 for Medium and 9/10 for Enterprise**. The Small sample observes one terminal timing point per run. The Enterprise sample has two years of hourly readings across 24 partitions and a fairly uniform installation layout. These scores describe how realistic the analysis is at each tier. Application performance needs a separate measurement.

Two further development checks give the other tiers concrete acceptance criteria.

- **Medium.** For December 2024 carrier invoices, keep the latest eligible version of each line received before February 1, 2025 UTC. Include lines still missing a matching manifested parcel. The result is **47 distinct invoice lines totaling $351.79** in signed charges.
- **Enterprise.** Compare issued bills at the frozen June 2024 billing close with signed debit postings through July 7 at noon UTC. Include unmatched sides for each invoice and account. For invoice **18023**, billed minus posted is **+$22,199.01** for account **23** and **−$22,199.01** for account **32**.

## Where the work lives

| Location | What it holds |
|---|---|
| Git repository | Project requirements, architecture and this sample guide. A fresh clone contains the documentation. |
| Development machine | The fixture kit at `~/.local/share/bramble-development/samples-v1/` holds the three databases, exports, detailed briefs, dictionaries, scoring notes, generators and reference SQL. Its `development/README.md` is the operator guide. |
| Evaluator storage | Nine fresh PostgreSQL databases cover three domains at all three tiers. The evaluator holds the public source snapshots, private prompts, answer keys and backups outside the checkout, and releases one read-only connection per trial. |

The two local storage areas currently share an operating-system owner. A separate evaluator account or machine is needed for a trial that also blinds the person who administers development.

On the prepared development machine, replay the three public answers with:

```sh
cd ~/.local/share/bramble-development/samples-v1
python3 evaluation/postgres/lab.py start
python3 development/check.py check
```

These checks validate the fixture answers. Comparing direct and delegated analysis requires a run of both routes against the same database and question. Long-term storage and distribution of the fixture kit are deferred.
