## UniForm 

UniForm (UForm) is unum.cloud library, which allows to embed media and texts written across numerous languages in unified vector space with state-of-the-art quality, but at the fraction of the cost, making Artificial Intelligence accessible for the next million applications.

Currently, there are two visual-language models available:

* Monolingual English model: BERT with 4 layers (2 unimodal layers and 2 multimodal layers) + ViT-B/16
* Multilingual model, supporting 11 languages (en, de, es, fr, it, jp, ko, pl, ru, tr, zh): BERT with 12 layers (8 unimodal layers and 4 multimodal layers) + ViT-B/16

## Usage

To load model use following code:

```python
import uform

model = uform.get_model('eng') # for the monolingual model
model = uform.get_model('multilingual') # for the multilingual model
```

To encode data:

```python
from PIL import Image

# data preprocessing
img = Image.open('red_panda.jpg')
img = model.img_transform(img).unsqueeze(0)
text = 'a small red panda in zoo'
text = model.text_transform(text)

# image encoding
image_embedding = model.encode_image(img)

# text encoding
text_embedding = model.encode_text(text)

# joint multimodal encoding
img_text_embedding = model.encode_multimodal(img=img, text_data=text)
```

To calculate the compatibility of the image and the text:

```python
# vectors normalization
image_embedding /= image_embedding.square().sum(dim=1, keepdim=True).pow(0.5)
text_embedding /= text_embedding.square().sum(dim=1, keepdim=True).pow(0.5)

# unimodal similarity (range [-1, 1])
scores = (image_embedding * text_embedding).sum(dim=1)

# multimodal similarity (range [0, 1])
scores = model.get_matching_scores(img_text_embedding)
```