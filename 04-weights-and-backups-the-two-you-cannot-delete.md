# Weights and backups: the two you cannot delete

Two stores cannot honour a delete, and for both the honest deliverable is a documented process plus a disclosed residual, not a deletion claim. Weights get exclusion from the next training run. Immutable backups get a rotation date and a suppression list that survives a restore.

## Trained weights

If a record was in the training set, its influence is spread across the parameters and no operation removes it with a guarantee.

The guarantee that does exist is narrow. Guo et al. (ICML 2020) define certified removal, a model from which data is removed being indistinguishable from one that never saw it, and what they build is "a certified-removal mechanism for linear classifiers". Linear classifiers. That does not transfer to a transformer.

The other real mechanism is a cost reduction on the complete remedy, not an edit to weights. Bourtoule et al. (IEEE S&P 2021) introduce SISA training, which "expedites the unlearning process by strategically limiting the influence of a data point in the training procedure", so a removal request retrains one shard instead of the whole model. It still retrains.

Approximate unlearning for large models is where the marketing lives. Cooper et al. (arXiv:2412.06966) map the gap between the goals of unlearning and feasible implementations, and conclude that "unlearning is not a general-purpose solution for circumscribing generative-AI model behavior". A method with no exactness guarantee is a mitigation. Do not write it into a deletion confirmation.

Regulators arrive from the other direction. EDPB Opinion 28/2024, adopted 18 December 2024, holds that anonymity claims about AI models are assessed case by case, given that "AI models trained with personal data cannot, in all cases, be considered anonymous".

So the honest remedy is three parts: remove the record from the training corpus and every dataset snapshot so the next run cannot include it; write down the residual, which checkpoints predate the request, what they saw and when they retire; and add output-side controls if the risk warrants. Call it exclusion with a retirement date, not deletion.

## Immutable backups

WORM locks, retention-locked snapshots and offsite archives are built so you cannot edit them. That is the feature; a delete request does not change it.

The regulator's expectation is a schedule, not an edit. UK ICO erasure guidance says that "It may be that the erasure request can be instantly fulfilled in respect of live systems, but that the data will remain within the backup environment for a certain period of time until it is overwritten", the key issue being to put the backup data beyond use where it cannot be immediately overwritten, and that "the backup is simply held on your systems until it is replaced in line with an established schedule".

That converts into three artifacts. A rotation date, computed from the longest retention class the record landed in, given to the requester as when it leaves the backup estate. The suppression list from chapter two, replayed as a required step in the restore runbook. And crypto-shredding where a per-subject key already exists, the one technical remedy that works on immutable media, and only if the key was per-subject before the request.

## The sentence you can defend

What is gone now, what is gone by a stated date, and what will not be gone, with the reason. Three clauses. That beats a deletion claim a restore or a checkpoint can falsify.
