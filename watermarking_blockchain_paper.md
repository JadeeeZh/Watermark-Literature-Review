# AI-Generated Content Watermarking and Blockchain-Based Royalty Distribution: A Breakthrough for On-Chain AI Verification

## Abstract

Generative AI models can produce text, images, audio and more at scale – bringing innovative opportunities but also raising issues of content authenticity, copyright ownership, and profit distribution [1,2]. **AI-generated content watermarking** has emerged as a key technique to embed hidden markers in AI outputs for source tracing and model identification. This paper presents a comprehensive overview of current watermarking methods for text, image, and audio content, and proposes using these watermarks to build an **on-chain content provenance system** that traces model usage and content dissemination. On this basis, we design a blockchain-based **royalty distribution mechanism**: by leveraging tamper-proof blockchain records and watermark detection, we enable automatic back-tracing of contributions and revenue sharing across multiple rounds of AI-generated content. We detail watermark embedding techniques, robustness and anti-tampering measures, digital signature integration, blockchain registration, and smart contract structures. We also analyze potential attack vectors and the technical feasibility of the system. Finally, we discuss the scheme's application prospects in the digital content industry, its business significance, and investment value – highlighting that lightweight watermark-based verification could be a breakthrough for bringing AI content on-chain (solving the impracticality of traditional heavy zero-knowledge proofs) and for streamlining royalty computations.

## 1. Introduction

Recent breakthroughs in generative AI (for chat, image synthesis, voice cloning, etc.) have heightened concerns about content authenticity and copyright attribution [1]. On one hand, AI-generated content can be misused for deepfakes and misinformation, eroding public trust in digital media [1]. For example, in 2023 an AI-generated image of Pope Francis in a puffy coat fooled millions of viewers before being revealed as fake [3], and criminals have used AI-cloned voices of company executives to commit fraud [4]. These incidents underscore the risks and societal harm when content provenance is unverified. On the other hand, large generative models are trained on vast internet datasets that include many copyrighted works, sparking an outcry from creators demanding compensation and attribution [2]. Artists and authors have filed lawsuits alleging that AI companies used their works without permission [2]. How to encourage technological innovation while protecting creator rights and ensuring AI-generated content's credibility has become an urgent industry and regulatory question.

To address these challenges, the industry has proposed embedding **digital watermarks** in AI-generated content. A **digital watermark** is a hidden identifier embedded imperceptibly into digital content to mark the content's source or owner [1]. Watermarking promises that, without affecting user experience, we can tag AI-generated text, images, audio, etc. with an invisible signature that is machine-detectable. In combination with distributed ledger (blockchain) technology, these watermark identifiers can be linked to on-chain content credentials (e.g. generator ID, timestamps, copyright info), creating an end-to-end content provenance framework [5]. As AI-generated content spreads online, anyone can extract the watermark and check the blockchain record to verify who (or which model) produced the content and through what transformations it has passed.

Going further, blockchain smart contracts enable automated royalty-sharing. Whenever AI-generated content is re-used, remixed, or commercially exploited, a smart contract can, based on the on-chain provenance graph and preset profit-sharing rules, automatically allocate revenue to relevant contributors (such as original data creators, model developers, downstream editors, etc.) [6,7]. This mechanism could resolve the current problem where **data providers receive no benefit** from generative AI – instead establishing a healthy AI content ecosystem where value flows back to original creators.

## 2. Technical Background

### 2.1 Digital Watermark Concept and Characteristics

A **digital watermark** is a technology for embedding hidden identifiers into digital content for copyright marking and content authentication [1]. A typical watermark system involves two processes: **watermark embedding** and **watermark detection/extraction**. In the embedding stage, the watermark encoder merges identifier information (e.g. owner ID, content ID) with the original content to produce a watermarked piece; in the detection stage, a decoder algorithm extracts or verifies the hidden identifier from the content to confirm its source or ownership [1,8].

An ideal digital watermark should satisfy several requirements:

- **Imperceptibility**: embedding the watermark should not cause perceptible quality degradation – the content should look or sound virtually identical to the original [9]
- **Robustness**: the watermark should remain detectable even if the content undergoes common transformations or attacks (re-compression, cropping, adding noise, format conversion, etc.) [1]. (Depending on sensitivity to modifications, watermarks are often classified as *robust* or *fragile*: a robust watermark survives moderate edits, whereas a fragile watermark is destroyed by even tiny changes and is used to detect any tampering.)
- **Security**: watermark schemes are usually controlled by secret keys – only those with the secret key can embed or detect the watermark. Unauthorized attackers should neither easily detect the presence of a watermark nor remove it without significantly degrading the content [1]. In essence, a secure watermark should be **unforgeable**: it should be computationally infeasible for anyone except the content producer (with the secret key) to create valid watermarked content or to alter a watermark undetectably [1]

### 2.2 Watermark vs. Digital Fingerprint

Digital watermarks are related to the concept of **digital fingerprints** (also called forensic watermarks), but there are differences. A traditional watermark often means embedding the **same identifier** in all distributed copies of a content – for example, marking all copies of an image with the owner's ID or the model's identifier. This serves to label the content's source or copyright info in general. A **digital fingerprint**, by contrast, embeds a **unique hidden ID in each individual copy** of the content, in order to trace which specific copy was leaked or misused [10,11]. For instance, a music label distributing a prerelease song could watermark each recipient's copy with a unique code; if one copy is illegally shared, the extracted fingerprint can identify which recipient was the source of the leak [10,11].

In our proposed system we focus primarily on watermarks for content source authentication (marking all AI outputs from a model with the model's ID or a content ID). However, the framework could be extended to include fingerprinting for tracing different distribution channels or individual users if needed – e.g. each platform adding its own layer of watermark to identify downstream re-distributors.

### 2.3 Multi-Modal Watermarking Techniques

Digital watermarking was first extensively studied in the context of static images and video, and those techniques are quite mature. Classic image watermark methods work by adding slight noise or adjustments in a transform domain – for example, modifying the magnitudes of selected frequencies in the image's discrete cosine transform (DCT) or wavelet transform, or altering least significant bits of pixels – to embed a bitstring of information [8]. These changes are calibrated to be visually imperceptible yet algorithmically detectable. Such methods can survive common image processing (e.g. JPEG compression, moderate blurring or scaling) fairly well, though they often have limited payload capacity (perhaps only tens of bits) and their design is manually tuned to specific transforms. In video, watermarks are typically embedded either per-frame or across sequences of frames, sometimes leveraging the temporal redundancy to improve robustness (e.g. repeating the watermark periodically so that even if some frames are lost or altered, the mark can be recovered from others). Today, more advanced strategies involve integrating watermarks into the generative model itself: for example, **Wen et al. (2023)** propose a "Tree-Ring Watermark" for diffusion image models that injects a special circular interference pattern into the initial noise used for image generation [12]. This causes every generated image to carry a hidden spectral pattern – by inverting the diffusion process, the watermark can be extracted. The Tree-Ring Watermark demonstrated high robustness to various image transformations (Gaussian blur, cropping, re-scaling, rotations, etc.) while remaining invisible to human observers [12,13].

For **audio watermarks**, techniques leverage properties of human hearing. One approach is to embed signals in frequency ranges that are less audible to humans (for example, very high frequencies beyond normal hearing, or in spectral regions masked by louder sounds) [14]. Another method is **phase modulation or echo hiding** – introducing slight phase shifts or low-level echoes that don't affect perceived audio quality but can be detected via signal analysis. The embedded watermark might be a pseudorandom noise sequence or spread-spectrum signal carrying the information bits. Audio watermark systems have been used in music and broadcast media to track unauthorized recordings. A well-designed audio watermark is **inaudible** to listeners yet survives transformations like re-compression (e.g. MP3 encoding), adding noise, or slight speed/pitch changes. For example, robust audio watermarks have been shown to survive MP3 compression at bitrates like 64 kbps and common filtering operations. However, if an adversary applies extreme transformations (re-encoding at very low quality, time-stretching audio, etc.), the watermark could be degraded. To counter this, strategies include multi-band embedding (spread the watermark across multiple frequency bands so that removing it entirely requires drastic distortion) and error-correcting codes (so that some watermarked bits can be lost but the message still recovered). It's worth noting that ensuring an audio watermark is completely imperceptible is challenging – the human ear can be sensitive to small perturbations, especially in music. Some research even explores **semantic** audio watermarks that hide in the content's structure (e.g. a barely noticeable melody or phase pattern) so that a detector can pick it up but a listener just perceives it as part of the audio.

**Text watermarking** is a newer and particularly challenging area. Because natural language is highly variable – the same idea can be phrased in countless ways – embedding an invisible mark in text that remains intact through paraphrasing or translation is difficult. Early approaches use zero-width or invisible Unicode characters inserted in the text (for example, placing zero-width spaces in specific pattern to encode bits). These are easy to implement but also easy to strip out or might be flagged by text sanitization, thus not robust. A more sophisticated approach, pioneered by OpenAI researchers, is to subtly bias the language model's word choice to encode a statistical pattern [15]. For example, the model can use a secret key to designate a subset of the vocabulary as "green-list" words and another subset as "red-list" words. During text generation, the model is nudged to preferentially pick green-list words at certain positions such that over a long passage, the distribution of green vs. red words reveals a hidden bit pattern to someone who knows the key [15]. This watermark is **spread across the text**: no individual word choice gives it away, but statistically the text betrays a signature when analyzed with the correct detector. The watermark is invisible to readers and preserves the text's fluency [15]. However, if an adversary paraphrases the text (changing many words to synonyms or altering sentence structure), these statistical markers can be disrupted [15]. Recent work by Google DeepMind (SynthID for text) similarly inserts an imperceptible pattern by fine-tuning the model's output probabilities, acting as a **logits processor** on top of any language model [16]. The goal is to embed a secret signal without significantly affecting the quality or style of the text. The main difficulty for text watermarks is **fragility**: even minor rewording or use of a different model to re-generate the text can erase the watermark. Improving the robustness of text watermarks – for instance, by encoding the mark in more persistent features like syntactic patterns or across longer spans of text – is an active area of research [15]. There is a trade-off: making the watermark signal stronger (and thus harder to remove) could make it more detectable or affect the text's naturalness, whereas making it subtle preserves quality but makes it easier to lose. Major AI companies and academic groups are rapidly iterating on text watermarking methods (OpenAI, DeepMind, etc.) including ideas like encrypting the watermark or using zero-knowledge proofs to allow public verification without revealing the key. We will discuss strategies to improve watermark robustness in later sections.

### 2.4 New Developments in Watermarking

Early watermarking techniques often used fixed algorithms designed by hand in specific domains (e.g. adding noise in an image's frequency domain or swapping synonyms in text) – these worked but might lack adaptability against evolving attacks. In recent years, **deep learning** has been applied to watermarking, enabling end-to-end optimization of an encoder–decoder pair for embedding and detection. By training neural networks to jointly learn how to hide a message and how to recover it after various distortions, these methods significantly improve watermark resilience to a wide range of attacks [1]. Google's **SynthID** (for images and text) and Meta's **VideoSeal** (for video) are examples of deep neural watermarking systems [1,17]. They use convolutional or transformer-based models to encode a digital signature directly into the content's pixel or waveform values, in a way that is imperceptible and can withstand operations like blurring, cropping, re-scaling, re-encoding, etc. [1,9]. These learning-based watermarks are often trained adversarially: during training, the watermarked content is subjected to a suite of "attacks" (simulated edits) and the decoder is tasked to still extract the correct watermark [1]. Through this process (analogous to adversarial training), the model learns to embed the watermark in the content's more resilient features (for example, in an image's spatial textures or an audio's timbral components) rather than in brittle bits. For instance, **Gunn et al. (2024)** propose an image generation watermark that uses pseudo-random error-correcting codes in the latent space of a diffusion model to embed an encrypted signature across multiple semantic levels of the generated image [18,19]. The resulting watermark is **undetectable** by anyone without the secret – even if an attacker collects many samples, they cannot statistically distinguish watermarked images from un-watermarked ones without the key. This means the watermark has effectively zero impact on perceptible quality, and attackers cannot even be sure a watermark is present, reducing the risk of targeted removal [18]. Moreover, by using a robust coding scheme, such approaches can embed a large payload (e.g. an ID plus timestamp, model ID, etc.) while maintaining stealth [18,19].

Another line of research focuses on **anti-forgery** and public verification. For example, **Liu et al. (2024)** propose an "unforgeable and publicly verifiable watermark for LLMs" by separating the watermark generation and detection processes and using different keys for each [20]. Even if the detection algorithm is made public, attackers would find it extremely hard to forge a valid watermark or strip an existing one without the secret key. This is achieved by leveraging cryptographic principles (like one-way functions) alongside deep learning, providing security guarantees under certain computational assumptions [1]. Such approaches aim to break the "cat-and-mouse" cycle of watermarks being broken and then improved by offering provable security properties: in theory, even a knowledgeable attacker cannot defeat the watermark unless they solve an intractable problem (or obtain the key). Though many of these schemes are still in academic exploration, they point towards more **reliable and trustworthy watermarking** in the future [1].

## 3. Watermark Techniques and Properties

Having covered the general concepts, we now examine current mainstream watermarking methods for different content modalities and compare their properties, laying the groundwork for our on-chain system design.

### 3.1 Text Watermark

Watermarking AI-generated text focuses on embedding barely perceptible patterns in a Large Language Model's (LLM) output. Two primary approaches have emerged:

**(a) Invisible character embedding**: inserting zero-width spaces or similar invisible Unicode characters in certain positions or sequences to encode information. This has the advantage of not altering visible text, but such characters are easily removed or flagged by text processors, so robustness is low.

**(b) Probability distribution encoding**: tweaking the model's word selection process to imprint a statistical signature. OpenAI's prototype (developed by Scott Aaronson) follows this approach: using a secret key, the model pseudo-randomly classifies vocabulary into "green" and "red" lists and biases toward green-list words when generating [15]. Over a long passage, the frequency pattern of those preferred words forms a hidden signal that a verifier (knowing the key) can detect by statistical tests [15,21]. This method is invisible to readers and doesn't significantly alter the text's fluency. However, text watermarks tend to be **fragile**: if an adversary paraphrases the text or even uses another LLM to re-generate the content, the watermark signal can be destroyed [15]. Research to improve text watermark robustness includes using larger n-gram patterns (marking sequences of words rather than individual tokens) or syntactic watermarking (influencing punctuation or sentence structure) to survive mild rewriting [15]. But there's an inherent trade-off: stronger signals (and detection reliability) often make the watermark easier to notice or remove by determined adversaries. Overall, text watermarking is still maturing. Big AI firms and scholars are actively exploring more resilient schemes, including combining watermarking with model encryption or leveraging zero-knowledge proofs for verification. For instance, Google's DeepMind has integrated a watermark (SynthID-Text) into its Gemini chatbot, which subtly alters word choices according to a secret scheme – this has been deployed to millions of users as a transparency measure [22]. Similarly, OpenAI has hinted at watermarking in its outputs, though details are not public [23,24]. We anticipate more robust text watermark techniques will emerge, perhaps with cryptographic assurances, to balance detectability and durability against paraphrasing.

### 3.2 Image Watermark

Digital watermarking is most established for images. Traditional methods embed watermarks in the image's frequency domain or other transform domains. For example, a simple approach might map the watermark bits to slight changes in certain DCT coefficients of an image block, or to flip specific least significant bits of pixels in a pseudo-random pattern [8]. These classical methods can often survive common image manipulations (lossy compression, moderate resizing, noise) and have been used in applications like photo copyright labeling and broadcast media. However, manually engineered algorithms usually carry only a small payload and can sometimes be defeated by targeted attacks (e.g. an attacker who knows the algorithm can apply a combination of filtering and noise to erase the mark). In recent years, learning-based approaches have gained traction. For example, a generative model (like a diffusion or GAN model) can be augmented with a watermark encoder so that **every image it generates contains a hidden watermark by design**. Google's **SynthID** is a prominent example: it adds an invisible, hard-to-remove pattern at the pixel level of AI-generated images [9]. According to Google, SynthID's mark does not visibly affect image quality but remains detectable after typical edits like cropping, re-scaling, or adding filters [9]. DeepMind's researchers strengthened the watermark's robustness by adversarial training – during development, they subjected watermarked images to a variety of transformations and fine-tuned the system to ensure the watermark could still be recovered [1]. It's reported that the SynthID watermark resisted many common image manipulations in tests [9,25]. To make removal even harder, the exact implementation details of SynthID have not been fully disclosed to the public [9]. This "security through obscurity" approach slows down attackers, though ultimately the goal is to rely on secure keys rather than secrecy of the algorithm. In a robust watermark design, even if an attacker knows the algorithm but **not the secret key**, they should be unable to forge or erase the watermark without severely damaging the image [1].

Besides images, **video watermarking** can be seen as a natural extension – one can watermark each frame and/or add a temporal component. Meta's **VideoSeal** tool, for instance, is tailored for video: it embeds an invisible signature across video frames using a deep learning model, optimizing for resilience against video-specific distortions (compression artifacts, frame rate changes, etc.) [1]. VideoSeal reportedly achieves robust detection even if the video is cropped or some frames are dropped, by leveraging redundancy over time. In summary, image and video watermarking techniques today can hide signatures that are virtually imperceptible yet can survive many transformations, making them powerful for tracing AI-generated visual media.

### 3.3 Audio Watermark

Audio watermarking typically leverages the fact that human hearing has **masking effects**: certain modifications to a sound wave are not noticed if they occur in ranges or moments where louder audio dominates. Common audio watermark methods include adding a very low-level broadband noise shaped to the host audio's spectrum (so it hides under the natural sound), introducing faint echoes or delays that create an imperceptible resonance, or tweaking the phase of audio samples in a consistent way. The embedded information might be a bit sequence spread out over the audio's duration or spectrum. For AI-synthesized speech or music, one can integrate these perturbations during the generation process. The challenge is ensuring the watermark remains through inevitable format conversions or user manipulations. A robust audio watermark should withstand re-sampling (changing sample rate), **lossy compression** (like MP3 or AAC encoding which can strip out some frequency content), addition of ambient noise, and simple speed or pitch adjustments. Some research has demonstrated watermarks surviving aggressive compressions and moderate noise levels. For example, a watermark may be designed such that even after MP3 compression at 64 kbps, the correlation detector can still pick up the hidden code. However, strong adversarial attacks (like filtering out certain frequencies or using audio editing software to "smooth" the waveform) can weaken the mark. To combat this, advanced schemes use **spread spectrum** signals – the watermark message is encoded as a pseudorandom sequence that spans a wide frequency range at low power, so removing it would require destroying large portions of the audio [8]. Some also use error-correcting codes to recover the message even if parts are lost. It's notable that the human ear's sensitivity means audio watermarks must be extremely subtle; a poorly implemented watermark that creates audible artifacts will be unacceptable (for instance, any audible hiss or tone could ruin a music track). Therefore, audio watermark research often emphasizes perceptual testing to ensure inaudibility. In commercial use, audio watermarks have been deployed in digital music and radio (one example: watermarks in Academy Award "screener" soundtracks to identify leaks). Compared to images, audio watermarking may be more challenging to do robustly without any perceptible effect, but it remains a crucial technique as AI-generated audio proliferates. Interesting recent developments include watermarking integrated with AI **vocoders** or speech synthesizers, where the model's output layer is constrained to produce watermarked audio from the start. This can potentially yield more hidden yet resilient marks, though it's an emerging area.

### 3.4 Multi-Modal & Metadata Watermark

Beyond altering the content signal itself, there are methods of content marking based on metadata. An example is the **C2PA standard (Coalition for Content Provenance and Authenticity)** [5], championed by companies like Adobe. C2PA allows producers to attach a cryptographic metadata "credential" to a file, recording information such as the content's creator, editing history, or whether it's AI-generated. This metadata is signed digitally to prevent tampering. Such an approach doesn't change the content pixels or waveform at all, so it has **zero impact** on quality and is straightforward to implement. However, the weakness is that metadata can be stripped or lost easily – a malicious actor can simply remove the attached metadata or copy the media into a new file without it [5,26]. Thus, a purely metadata-based provenance label is fragile in adversarial scenarios. A practical strategy is to **combine invisible watermarks with metadata signatures**: the watermark is embedded in the content as a hidden backup, while the metadata provides a convenient, human-readable provenance trail. If the metadata is removed, the invisible watermark is still in the content and can be used to retrieve an identifier. This two-layer approach enhances reliability [5,26]. For instance, **Numbers Protocol** (a blockchain-based media authenticity platform) does the following: when a photo is captured, they immediately create a hash "fingerprint" of it and register it on-chain; they also embed a C2PA metadata block in the image file pointing to that on-chain record [5,27]. If someone strips the metadata, one can fall back to computing the image's hash or detecting a hidden watermark to match it against the blockchain database. In summary, watermarking is a crucial tool for content authenticity but often works best in concert with other measures like cryptographic hashes, digital signatures, and blockchain records [5,1].

A watermark can serve as a **content-level ID tag**, while the blockchain serves as an **immutable registry** of those IDs and their associated metadata. This multi-pronged strategy provides defense in depth against malicious manipulation.

With the above survey of watermark techniques and their properties, we can appreciate the strengths and limitations of each modality's approach. In the next section, we discuss how these watermark methods can be incorporated into a blockchain-based architecture to achieve secure provenance tracking for AI-generated content, and how an accompanying royalty mechanism can be built on top of that.

## 4. On-Chain System Architecture

Figure 1 below illustrates the overall architecture of our proposed on-chain provenance system and the information flow between its components:

```
┌─────────────────────────────────────────────────────────────────────┐
│                    AI-GENERATED CONTENT PROVENANCE SYSTEM             │
└─────────────────────────────────────────────────────────────────────┘

STEP 1: CONTENT GENERATION & WATERMARKING
┌──────────────────┐
│  AI Model /      │
│  Creator         │───────┐
└──────────────────┘       │
                           ▼
                    ┌─────────────────────┐
                    │  Generate Content   │
                    │  with Embedded      │
                    │  Watermark ID       │
                    └──────────┬──────────┘
                               │
                               ▼
STEP 2: BLOCKCHAIN REGISTRATION
                    ┌─────────────────────┐
                    │  Register Content   │
                    │  ID & Metadata      │
                    │  on Blockchain      │
                    │  (Provenance Record)│
                    └──────────┬──────────┘
                               │
                               ▼
STEP 3: CONTENT DISTRIBUTION
                    ┌─────────────────────┐
                    │  Content Shared/    │
                    │  Published to       │
                    │  Users/Platforms    │
                    └──────────┬──────────┘
                               │
                               ▼
STEP 4: PROVENANCE VERIFICATION
                    ┌─────────────────────┐
                    │  Extract Watermark  │
                    │  ID from Content    │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │  Query Blockchain   │
                    │  using Content ID   │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │  Return Source &    │
                    │  History Info       │
                    │  (Model, Creator,   │
                    │  Timestamp, etc.)   │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │  Display Provenance │
                    │  & Attributions     │
                    └─────────────────────┘

STEP 5: DERIVATIVE CREATION (Optional)
┌──────────────────┐
│  Derivative      │
│  Content Input   │───────┐
└──────────────────┘       │
                           ▼
                    ┌─────────────────────┐
                    │  New AI Model/      │
                    │  Editor Generates   │
                    │  Derivative         │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │  Embed New          │
                    │  Watermark ID       │
                    │  (links to parent)  │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │  Register New ID    │
                    │  with parent        │
                    │  reference on       │
                    │  Blockchain         │
                    └─────────────────────┘

┌─────────────────────────────────────────────────────────────────────┐
│              ROYALTY DISTRIBUTION (Smart Contract)                    │
│                                                                       │
│  When content generates revenue (sale, licensing, ad revenue):       │
│  1. Smart contract reads provenance graph from blockchain            │
│  2. Calculates royalty splits based on recorded percentages          │
│  3. Automatically distributes payments to all contributors           │
│     (original creator → intermediate editors → current owner)        │
└─────────────────────────────────────────────────────────────────────┘
```

**Figure 1: Architecture of the watermark-based on-chain content provenance system.** When a generative AI model produces content, it embeds a watermark containing a unique content identifier (ID). That ID, along with relevant metadata, is immediately recorded on the blockchain (provenance registration). If the content is later edited or used to create new content, the new AI (or user) preserves the original watermark or embeds a new one, and links the new content's record on-chain to its source. When the content is disseminated to end-users or platforms, anyone can extract the watermark ID and query the blockchain to retrieve the content's origin, its full creation history, and associated rights information. This enables verification of source and tracking of derivative lineage through immutable records. Smart contracts can then use the on-chain records to automate royalty settlements based on the provenance graph.

### 4.1 Content Registration & Watermark Embedding

Whenever an AI model (or human creator) generates a new piece of content, the system first assigns it a globally unique identifier – this could be a UUID or, more securely, a cryptographic hash of the content file. Next, a watermark corresponding to this ID is embedded into the content using one of the techniques discussed (text watermark for text, image watermark for images, etc.). For example, if the model generates an image, the system might embed a certain pseudo-random pattern in its pixel domain representing the ID; if the output is text, it might apply a lexical watermarking with a key tied to the content ID; if audio, it adds an inaudible signal encoding the ID. Crucially, this watermark is invisible to users but will later allow anyone to recover the content ID from the content itself. After embedding, the system creates a **blockchain transaction** to record the content's provenance metadata [5]. This on-chain **content record** would typically include: the content's unique ID (or a hash), the creator or generating model's identity, a timestamp of creation, possibly the prompt or source data ID used (if such information is to be shared), and any initial usage rights or license terms (e.g. "© Creator, non-commercial use only" or an NFT ownership token reference). The transaction is sent to the blockchain (which could be a public blockchain or consortium ledger) and once confirmed, it provides an immutable, timestamped proof of the content's origin. It's important to note that we do **not** need to store the actual content file on-chain – we only store a hash or ID and metadata. The content itself can be stored off-chain in a distributed storage network (like IPFS/Filecoin) or a cloud server, with the content hash ensuring integrity [5]. By comparing a file's hash to the on-chain hash, one can verify the file hasn't been altered. Thus, at the moment of creation, every AI-generated content gets a kind of "birth certificate" on the blockchain and a hidden "ID tag" within the content via the watermark.

### 4.2 Content Dissemination & Provenance Query

When watermarked AI content circulates on the internet or is shared to a platform, any recipient can verify its provenance by extracting the watermark. For instance, suppose a user encounters an image or audio clip and wonders if it's AI-generated and from which model. They can use a detection tool (this could be built into social media platforms, browser extensions, or standalone apps) to scan the content for a watermark. If a watermark ID is found, the tool then queries the blockchain (either by searching for that content ID or calling a smart contract function) to retrieve the corresponding provenance record [5]. The blockchain lookup will return the stored metadata: e.g. "Content ID X was generated by Model Y (version Z) at time T; Creator=Alice's AI Studio; Original hash H; License=CC-BY-NC," etc. It may also indicate whether the content was derived from prior content (more on that shortly). This allows the viewer to confirm **who or what model produced the content and when**. If the chain record shows it's AI-generated by a particular model, the user or platform can then, for example, display an "AI-generated" label or decide to treat it accordingly. This automated provenance check is valuable for **content authenticity** – e.g., a news outlet receiving a photo can verify if it comes from a known journalist's camera (with a registered record and no AI watermark) or if it was AI-generated by some model, helping to prevent deceptive deepfakes in journalism [9,25]. For regulators or copyright enforcers, the system provides a transparent way to trace content: if infringing AI content appears, one can find out which model made it (and potentially which data it used, if those details are recorded or can be further queried). Importantly, because the watermark is embedded in the content itself, the provenance stays attached to the content wherever it goes. Anyone, even years later, can perform the check as long as the blockchain records persist.

### 4.3 Authenticity Verification & Tamper Detection

Because content can be tampered with (including malicious attempts to remove or alter a watermark), the architecture includes mechanisms for authenticity verification using the blockchain records. One straightforward measure is storing the content's cryptographic hash on-chain as part of the provenance record. When a watermark is extracted, one can compute the current content file's hash and compare it to the on-chain hash recorded at creation. If they differ, it means the content has been modified since it was registered (or it's not the exact original file). This acts as an integrity check – for example, if someone tries to subtly alter a watermarked image (without completely destroying the watermark) and pass it off as the original, the hash mismatch will reveal the change. Another sophisticated approach proposed by industry (e.g. by Digimarc) is to embed not just an ID but also a **content fingerprint within** the watermark itself [28,29]. For instance, each video frame's watermark payload might include a hash of that frame's pixels. If an attacker alters the frame, the extracted hash won't match the actual frame content, signaling tampering. The blockchain record could store expected hashes or even AI-generated "reference features" (like key image descriptors) of the original content [28]. Upon verification, one compares the current content's features to the recorded ones. If there's a discrepancy, it indicates manipulation. This dual approach – watermark + on-chain hash – creates a **belt and suspenders** security model. Even if an attacker manages to **forge a watermark** or copy a valid watermark from one content to another, they would fail to produce a matching hash to the blockchain record, since the content itself differs. The immutable timestamped nature of blockchain also helps mitigate certain attacks: for example, an attacker cannot easily create a fake chain record retroactively with an earlier timestamp to claim origin, nor can they alter an existing record to cover their tracks. Every update (like a derivative registration) is append-only and time-stamped, providing a chronological audit trail. In case of dispute (say someone claims a piece of content is human-made when chain says AI-generated), the combination of watermark evidence and blockchain proof-of-record can serve as a **strong digital witness**. Users can always rely on the chain data because it's decentralized and tamper-proof, unlike a centralized database that could be hacked or altered. In summary, the on-chain provenance architecture leverages watermarks as invisible "content passports" and blockchain as a secure "passport registry": the watermark travels with the content, and the blockchain provides a trustworthy database to validate those passports. Together, these enable open-world verification of digital content origin and integrity in a way that was previously not possible.

## 5. Royalty Distribution Mechanism

Building on the content provenance foundation, we introduce a **royalty distribution mechanism** that ensures participants in the AI content creation chain are compensated fairly whenever the content generates value [6,7]. This mechanism has two main parts: on-chain licensing records and automatic revenue splitting via smart contracts.

### 5.1 On-chain License Registration

When a piece of content is registered on-chain (as described above), the creator or rightsholder can attach a digital copyright notice or usage license terms to its blockchain record [6]. This could be implemented as part of the content's metadata or via a separate smart contract that maps content IDs to royalty rules. For example, suppose Alice creates image A (ID=A) using her AI model. She might specify on-chain: "If this image (or its derivatives) is used commercially or sold, 5% royalty goes to Alice." Another example: a model developer might declare that any output from Model X carries a baseline royalty of 2% to the model's owner. To facilitate interoperability, these terms should use a standard schema – for instance, a **programmable license template**. The emerging **Story Protocol** has proposed a concept of programmable IP licenses (PIL) [30] that allow creators to encode permissions and royalty percentages for derivatives. Using such a template, Alice could signal that derivative works are allowed as long as a certain revenue share is paid. All such license and royalty data is anchored on-chain alongside the content's provenance. Importantly, the blockchain record will also record the **current owner or royalty recipient address** for the content [5]. This could simply be the wallet address of the original creator by default, but it could be transferable (for example, if Alice sells the rights to Bob, the on-chain owner could be updated to Bob's address). By having each content's rights and royalty info on-chain, we enable smart contracts to later automatically calculate payouts by reading those records.

### 5.2 Triggering Events & Royalty Computation

When a content is monetized – for instance, sold as an NFT, licensed for use in a game, or generating ad revenue on a platform – an event is sent to the blockchain to share the revenue among entitled parties. Take a simple case: content C (ID=C) is a derivative of B (parent) and A (grandparent). Now someone purchases content C's NFT for 100 tokens, or perhaps content C generates 100 tokens of advertising revenue on a platform. We have on-chain rules (from earlier registrations) that say, for example, "A gets 5% of derivative sales" and "B gets 10%" [6]. A specialized **royalty smart contract** is deployed which knows how to traverse the content lineage graph and gather applicable royalties. When the sale occurs, the platform or marketplace contract would call the royalty contract, passing in content C's ID and the payment amount (100). The royalty contract looks up C's on-chain record to see if it has any parent content. It finds B as parent and A as grandparent via the links [6,7]. It then looks up B's royalty rule (say B's creator set 10%) and A's royalty rule (5%) [6]. It sums these into a total royalty obligation (in this case 15% of the sale). The contract would then split the 100 tokens: 5 tokens routed to A's registered payout address, 10 tokens to B's address, and the remaining 85 tokens to the seller (C's owner). This forms a **"royalty stack"** – each ancestor in the content's provenance chain contributes a slice according to their defined percentage [7]. If any content in the chain had opted out of royalties (e.g. declared a public domain or 0% royalty license), the contract would note that as 0%. If multiple rules apply (say the model demands 2% and the artist demands 5% on the same piece), those could be aggregated or given priority according to the license agreements (potentially handled via more complex logic or tiered contracts). The entire calculation and transfer of funds happens automatically and near-instantly on-chain once triggered. By programming this into a contract, we remove the need for manual royalty accounting or trust in intermediaries – creators get their share directly according to code.

### 5.3 Royalty Vaults and Withdrawals

To manage continuous or streaming revenue (for instance, a piece of content earning ad revenue over time), we can use on-chain **royalty vaults** for each content. Essentially, each content or creator can have an escrow pool in the smart contract where their accrued royalties are collected. Every time a royalty payment is triggered for content A, instead of transferring to A's owner immediately (which could be many small micro-transactions), the funds could accumulate in A's "vault". The owner of A (the address on record) can then withdraw from their vault whenever they choose [5]. This approach is useful if there are frequent tiny transactions – it reduces gas fees by not forcing a token transfer every single time, at the cost of the owner having to call a withdraw function occasionally. The vault concept also allows us to introduce **royalty tokens**. Each content could be associated with a fixed supply of tokens that represent fractional entitlement to its royalty stream [5]. For example, when content A is created, the contract could mint 100 "A-royalty tokens", each representing 1% of the royalty earnings from A. Initially, these tokens belong to A's creator. The creator could then sell some of these tokens to investors or collaborators. Whoever holds these tokens can claim the corresponding percentage of the funds in A's royalty vault [5]. This essentially **tokenizes the future royalty flow** of content A, allowing creators to **monetize upfront or share ownership**. For instance, an AI model developer might sell 30% of the royalty tokens for the model's outputs to raise capital; buyers of those tokens would then automatically receive 30% of any royalties generated by that model's outputs (the contract would split the model's vault accordingly). Story Protocol's design alludes to such **royalty-bearing tokens or NFTs** to enable investment in IP rights [30]. Our system can support this by tying the vault payout to token ownership. All the accounting is transparent on-chain: one can see how much revenue has been accumulated and which addresses or token holders have what shares.

### 5.4 Multi-Party Split Example

Consider a complex scenario to illustrate the logic. Original image A (by Alice) sets 5% royalty. Image B is created from A by Bob; Bob sets an 8% royalty on B. Then image C is generated by Charlie using B (and indirectly A), and Charlie sets 10% on C (this might apply to further derivatives if any). Now, if image C is sold for 1000 tokens, the smart contract sees: A deserves 5%, B deserves 8% – totaling 13%. It will therefore allocate 50 tokens to A's vault (5%), 80 tokens to B's vault (8%), and the remaining 870 tokens to C's owner (Charlie) [6]. Alice and Bob can withdraw their earnings from their respective vaults at leisure. If image C later generates another 500 tokens (say licensing fee), the same percentages apply: 25 to Alice, 40 to Bob, 435 to Charlie. This way, even upstream contributors who are **two steps removed** still benefit from downstream success. The process is fully **automated and transparent** – all transactions are recorded on-chain, so each party can verify they got the correct share. This **multi-round revenue backtracking** ensures that "whoever contributed gets to share the reward," which could solve the current pain point where data providers or intermediate creators get nothing when their work is remixed by AI. Instead, they would receive ongoing royalties, creating a positive incentive loop. From an implementation perspective, one can imagine each piece of content as an NFT that has associated royalty rules and beneficiary addresses. Many NFT marketplaces already support automatic royalty payouts for secondary sales (commonly used to give artists a cut of resales); our system generalizes this across **arbitrarily many "generations"** of content via on-chain provenance. The key difference is the chaining of royalties: NFT standards like ERC-721 typically handle a royalty to the immediate creator, whereas here the contract recursively distributes to **all relevant predecessors** based on their terms. This is what Story Protocol and similar projects are aiming to achieve, and we provide a concrete scheme to do so [30].

In summary, by marrying watermark-based provenance with programmable smart contracts, we build an **automated, fair royalty distribution network** that embodies the principle "whoever contributes to an AI-generated work is rewarded." This benefits creators (who are no longer cut out of the value chain) and also investors (who can fund creative endeavors in return for a slice of future earnings via royalty tokens) [7]. It introduces new business models: for example, a photographer could allow her images to be used in model training in exchange for on-chain royalties from any model outputs that include her work – turning AI from a threat into a source of passive income. We will next consider the technical and security challenges in implementing this system and how to mitigate them.

## 6. References

[1] Cloudflare Blog. "AI-generated content watermarking and authentication." blog.cloudflare.com

[2] Reuters. "Artists sue AI companies over copyright infringement." reuters.com

[3] CBS News. "AI-generated Pope Francis image fools millions." cbsnews.com

[4] Avast Blog. "Criminals use AI voice cloning for fraud." blog.avast.com

[5] Numbers Protocol. "Blockchain-based content provenance." numbersprotocol.io

[6] arXiv Labs. "Automated royalty distribution for AI content." ar5iv.labs.arxiv.org

[7] arXiv. "Multi-round revenue sharing mechanisms." arxiv.org

[8] arXiv. "Digital watermarking techniques." arxiv.org

[9] The Verge. "Google's SynthID watermark for AI images." theverge.com

[10] Custos Tech. "Digital fingerprinting for content tracking." custostech.com

[11] Custos Tech. "Forensic watermarks explained." custostech.com

[12] Wen et al. (2023). "Tree-Ring Watermark for diffusion models." arXiv

[13] arXiv. "Tree-Ring Watermark robustness analysis." arxiv.org

[14] Reddit. "Audio watermarking frequency techniques." reddit.com

[15] TechCrunch. "OpenAI's text watermarking approach." techcrunch.com

[16] Spectrum IEEE. "Google DeepMind SynthID for text." spectrum.ieee.org

[17] The Verge. "Meta's VideoSeal watermarking system." theverge.com

[18] Gunn et al. (2024). "Undetectable watermarks for image generation." arXiv

[19] arXiv. "Pseudo-random error-correcting codes in watermarks." arxiv.org

[20] Liu et al. (2024). "Unforgeable watermark for LLMs." Cloudflare Blog

[21] TechCrunch. "Statistical watermark detection methods." techcrunch.com

[22] Spectrum IEEE. "DeepMind watermark in Gemini." spectrum.ieee.org

[23] Life Architect. "OpenAI watermarking hints." lifearchitect.ai

[24] Life Architect. "Transparency in AI outputs." lifearchitect.ai

[25] The Verge. "SynthID detection after image edits." theverge.com

[26] Numbers Protocol. "Metadata vs watermark comparison." numbersprotocol.io

[27] Numbers Protocol. "Photo fingerprinting on blockchain." numbersprotocol.io

[28] arXiv. "Content fingerprint within watermarks." arxiv.org

[29] arXiv. "Digimarc watermarking approach." arxiv.org

[30] arXiv Labs. "Story Protocol programmable IP licenses." ar5iv.labs.arxiv.org
