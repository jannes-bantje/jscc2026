
# AI@TNG
Jannes Bantje, Dominikus Hellgartner (TNG)

This was an ad hoc replacement for the AI showcases that were shown on Friday: less entertaining, but informative about our daily work with AI.

## Inference as a supply chain problem
* There has been a lot of recent news suggesting that relying on US providers for AI inference is a problem:
  * Very recently, Fable was pulled
  * GitHub Copilot got a lot more expensive
  * Claude subscriptions got more restrictive
* At TNG, we try to mitigate this by running our own GPU cluster and using open-source models for inference.


## TNG Skainet

We operate our own GPU cluster called “Skainet”, which at the time of writing consists of:
* 8x8 RTXPro 6000
* 1x8 H200
* 3x8 H100
* 8x8 B200 (rented, located in Paris)
* 1x8 AMD MI325x

It started with GPU servers in 2017 in the cellar of our office.
It is now a full-blown Kubernetes setup, with the hardware colocated in a data center (the electrical infrastructure in the office was no longer sufficient). Despite the cost of this hardware, we are still “GPU poor”.

Examples of models that we run on Skainet (at the time of writing):
* GLM 5.1
* Qwen 3.5
* DeepSeek V4-Flash
* Gemma 4
* GPT OSS
* Mistral Small
* Our own models (discussed later)


## Reasons for running your own GPU cluster
Apart from gaining some independence from US providers, there are other benefits for a consulting company like TNG:
* Consultants can do agentic coding in projects with high infosec requirements that would not allow the use of US providers.
* We can freely apply AI even to sensitive internal data.
* We build up expertise in running and maintaining GPU clusters, which is a valuable skill that we can also sell to clients.
* We can do some AI research (inference by day, research by night).


## AI proxy and TAIA
* Skainet is integrated into a general AI infrastructure that also provides access to all external providers and models.
* We have a token self-service for the AI services, which is super helpful when building AI tools and PoCs.
* A major consumer of these AI services is TAIA, the TNG AI Assistant, which is basically a chatbot interface to all the AI services we have. It is heavily focused on privacy:
  * Deep research in Confluence and all other features that rely on access to internal data are only possible with Skainet models.
  * Conversations are stored locally in the browser.
  * Personal data can be masked when using external providers.
* TAIA is available on the [StackIT marketplace](https://marketplace.stackit.cloud/en/products/taia-tng-s-secure-ai-9fb8b62b). You can try it out here: https://register.taia.stackit.gg/

## AI research
We are still far away from having the resources needed to train a model from scratch, but restricted resources boost creativity. Some things we have done:

* Fine-tuning: OCR for special purposes, adding reasoning to a non-reasoning model
* Lobotomy of mixture-of-experts models (removing the censorship from Chinese models)
* [Assembly of experts](https://arxiv.org/abs/2506.14794): we have built chimeras of different DeepSeek models (R1 and V3)  that show surprisingly good behaviour. Our R1T2 model was quite successful on [OpenRouter](https://openrouter.ai/tngtech) for some time in 2025.
* We have newer models, but the EU AI laws make us reluctant to release them (they fall into the highest-risk category)
* Recently, we have also started experimenting with robot foundation models: for example, teaching a robot to play Counter-Strike (see https://www.youtube.com/watch?v=VhMdJOllkyw)


## TWAIN: TNG Web AI Navigator

As a showcase of internal tools that rely on our AI infrastructure, we showed TWAIN, the TNG Web AI Navigator. It is a browser extension that allows you to use AI on any website. You can do the usual thing of asking questions about the current webpage in a privacy-focused way, but in a newer version you can give the extension much more agency by allowing it to interact with the page and perform actions on your behalf. This also includes making changes to the page.

