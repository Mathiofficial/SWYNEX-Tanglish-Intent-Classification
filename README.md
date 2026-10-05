# SWYNEX-Tanglish-Intent-Classification

**Task 1: AI Problem Design** | SWYNEX Technologies Internship
**Author:** Mathiyazhagan A

**Title:** Intent Classification of Tanglish (Tamil-English) Customer Messages for Small Local Businesses

---

## 1. Problem Statement

Automatically detect the intent of short customer messages written in Tanglish, meaning Tamil typed in English letters and mixed with English words, so that small businesses can sort and answer them faster.

Small shops, home bakeries, tuition centres and tailoring units in Tamil Nadu receive most customer queries on WhatsApp or Instagram. Messages look like "anna cake eppo ready aagum?", "delivery innum varala, enna achu?" or "2 kg biriyani rate evlo?". The owner reads every message by hand, and urgent complaints get buried between routine questions. Standard English text classifiers fail on this kind of text because the same Tamil word can be spelled many ways (for example "evlo", "evvalavu", "ewlo").

- **AI task type:** Multi-class intent classification on short code-mixed text (narrow, single-purpose).
- **Out of scope:** Replying automatically, order processing, speech or voice notes, and pure Tamil-script messages.

## 2. Target User

| Item | Description |
|---|---|
| Primary user | Owner of a small local business who handles customer chats personally |
| Secondary user | A helper or staff member who answers messages on the owner's behalf |
| User need | See each incoming message tagged with its intent, so complaints and order requests are handled first and routine price or timing questions can be answered quickly |
| How it is used | As a tag or label shown next to each message in a simple dashboard or exported chat sheet; the owner can correct a wrong tag, and the correction is saved for later retraining |

## 3. Data Source

| Item | Description |
|---|---|
| Input | One short customer message (1 to 30 words) in Tanglish, English, or a mix of both |
| Intent labels | Order request, Price enquiry, Delivery or status check, Complaint, Feedback or thanks, Other |
| Source | A small dataset built for this task: messages written by the author and volunteers (friends, family, local shop owners who agree to share), plus synthetic variations that cover different spellings. A public Tamil-English code-mixed dataset (for example the DravidianCodeMix shared-task data) can be used only for pre-training or comparison, since its labels are for sentiment, not these intents |
| Size (target) | About 1,500 labelled messages, with at least 150 per intent |
| Labelling | Two people label each message independently; disagreements are resolved by discussion, and agreement is measured with Cohen's kappa |
| Split | 70% train / 15% validation / 15% test, stratified by intent. Near-duplicate messages are kept in the same split to avoid leakage |
| Privacy | Phone numbers, names and addresses are masked. Only text shared with consent is used |

## 4. Constraints

- **Small data:** Only about 1,500 examples, so large models cannot be trained from scratch.
- **Spelling variation and code-mixing:** There is no standard spelling for Tanglish; one word can have many forms, and users mix Tamil and English inside one sentence.
- **Short text:** Messages are short and may contain emojis, slang and missing context.
- **Class imbalance:** Some intents (such as Complaint) will have fewer examples than others.
- **Compute and cost:** It must run on a laptop or free Colab tier, and be cheap enough for a small business to use.
- **Latency:** Tagging should take under a second per message.
- **Privacy:** Customer messages are personal, so no data leaves the owner's control and personal details are masked.
- **Safety:** Wrong tags are only suggestions; low-confidence messages are marked "Other / check manually".

## 5. Proposed Approach

1. **Normalisation:** lowercase, reduce repeated letters ("romba" and "rombaaa"), map common Tanglish spelling variants to one form using a small lookup list, and handle emojis.
2. **Baseline:** character n-gram TF-IDF with Logistic Regression. Character n-grams handle spelling variation better than word features.
3. **Improvement:** fine-tune a small multilingual model that supports Indian languages (for example MuRIL or XLM-RoBERTa base) and compare it with the baseline.
4. **Decision rule:** if the top probability is below a threshold (for example 0.6), mark the message for manual check.
5. **Feedback loop:** store owner corrections and retrain periodically.

## 6. Success Criteria

| Metric | Target | Why it matters |
|---|---|---|
| Macro F1 (intent) | at least 0.80 | Treats rare and common intents equally |
| Recall on Complaint | at least 0.90 | Unhappy customers must not be missed |
| Accuracy on spelling-variant test set | drop of no more than 5 points vs the normal test set | Shows the model handles Tanglish spelling variety |
| Inter-annotator agreement (kappa) | at least 0.75 | Confirms the labels are reliable |
| Inference time | under 1 second per message | Practical for live chats |
| Manual-check share | below 15% of messages | Keeps the tool worth using |

## 7. Evaluation Approach

- **Offline evaluation:** Report accuracy, per-intent precision, recall, F1, macro F1 and a confusion matrix on the held-out test set. Compare the baseline against the fine-tuned model.
- **Spelling-variant stress test:** Build a second test set where the same messages are rewritten with different spellings, mixed English and added emojis, and measure the drop in performance.
- **Error analysis:** Review the most-confused intent pairs (for example Delivery check vs Complaint) and 50 random errors to find patterns.
- **Language-mix slices:** Check whether results differ between messages that are mostly English and those that are mostly Tamil.
- **Threshold tuning:** Plot coverage against accuracy for different confidence thresholds and choose the best trade-off.
- **Pilot (future):** Let one or two shop owners use the tags on a real week of messages and record how often they correct them.

## 8. Risks and Mitigations

| Risk | Mitigation |
|---|---|
| Model learns only the author's writing style | Collect messages from several volunteers of different ages and regions |
| Inconsistent labels | Two annotators, written label guide, kappa check |
| Complaints mistaken for routine status checks | Prioritise recall for Complaint; use class weights |
| New slang and spellings appear over time | Save corrections and retrain periodically |
| Privacy of customer chats | Consent, masking of personal details, local processing |

## 9. Expected Outcome

A lightweight tagging tool that understands the way local customers actually write, helps small business owners spot complaints and orders quickly, and keeps the owner in control of every reply.

## 10. Next Steps

1. Write the label guide and collect the first 500 messages.
2. Train the character n-gram baseline and record metrics.
3. Fine-tune a multilingual model and compare.
4. Build the spelling-variant test set and document the results and error analysis.
