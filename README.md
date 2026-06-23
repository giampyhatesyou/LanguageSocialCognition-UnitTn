# Language and Social Cognition

This is my Language and Social Cognition project at the University of Trento. The question behind it is easy to state: when a language model agrees that a statement is true, does it stay consistent once you show it the negation or an antonym, or does it just go along with whatever it is shown? That second behaviour is the confirmation-bias-like failure I set out to measure.

## Setup

I built a set of 150 item triplets. Each triplet has a base statement A, its negation (not-A), and an antonym version, with ground-truth labels for all three. The model under test is GPT-4o, served through Azure and queried at temperature 0 so the answers stay deterministic. For every statement the model returns a True, False, or Insufficient info label together with a confidence score from 0 to 100.

I ran the whole set under three prompting modes. Neutral asks for a plain truth judgment. Logical spells out the rule that if A is true then not-A has to be false. Direct Contrast shows the model A and not-A side by side.

## What I measured

Accuracy on each statement type, and then triplet accuracy, which is stricter: an item only counts as correct if the model gets A, not-A and the antonym all right at the same time. On top of that I looked at logical consistency (how often A and not-A get the same label, or A and its antonym do), the abstention rate (how often the model answers Insufficient info), and calibration through the Brier score, the Expected Calibration Error, and a reliability diagram that compares stated confidence to real accuracy.

## What came out

The prompting mode mattered a lot. With a neutral prompt the model was strong on the plain statement A, around 96 percent, but it fell apart on the rest, and triplet accuracy was only about 49 percent. Asking for logical reasoning or showing the contrast fixed most of that.

| Mode | Triplet accuracy |
| - | - |
| Neutral | 49.2% |
| Logical | 91.7% |
| Contrast | 94.8% |

Consistency tells the same story. In Neutral mode the contradiction and antonym error rates were high, roughly 0.36 and 0.42, which the analysis flags as poor. Logical and Contrast pulled them down to about 0.02 for contradictions and under 0.10 for antonyms. There is a catch, though: the stricter prompts also pushed the model to abstain more often, so part of the consistency it gains comes from refusing to answer. Calibration looked solid throughout, with a low Brier score around 0.025 and an ECE around 0.007, and only a small gap between the model's confidence and its actual accuracy.

The short version: out of the box GPT-4o is happy to call both a claim and its opposite true, but a prompt that makes the logical constraint explicit removes most of that inconsistency, at the cost of more abstentions.

## Stack

Python, pandas, NumPy, Matplotlib, and the Azure OpenAI API for GPT-4o. The calibration metrics (Brier, ECE, reliability diagram) are computed by hand in the notebook.

## Files

`Confirmation Bias.ipynb` runs the whole pipeline: the prompt templates, the query and parsing logic, the experiment loop with periodic saving, and the accuracy, consistency, abstention and calibration analyses.

## Team

Group project, split evenly. I worked mainly on the technical and statistical side.
