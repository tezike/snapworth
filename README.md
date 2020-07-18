# snapworth
The Snapworth Project (Journey)

## AIM

The sole aim of this project is to be able to take a picture and have the price of the items in the picture shown to you. I've boiled the task down to obtaining
  - obtaining a database of all common images of everything(essentially be a google)
  - extracting the embeddings of all the images using the body of a convnet(resnet50) pretrained on IMAGENET has been used so far
  - saving these embeddings in any database(for now i'm using lists stored in gdrive)
  - As a user takes a picture, a segmentation model segments out the important categories in the picture that we desire and lables everyother thing as backgroud (painted white) [Yet to be implemented]
  - Each segmented category is then passed through it's appropriate feature extractor to obtain it's features
  - These features are then used to index our db to find the most similar using cosine similarity distance
  - The the prices of the n most similar images are obtained and an average of these is assumed to be the price of that particular segmented category
  
## Drawbacks

  A major hitch i've faced so far is obtaining a dataset that is representative of every type of picture people can take. So far, i've tested the approach listed above for images of cars and women's fashion and it works as planned even for images it has never seen. Getting an appropriate dataset is quite impossible IMHO. The approach i have seen being used by some e-commmerce retailers is to finetune the problem down to their domain(their ecommerce platform). A platform like say Jumia may decide to implement this feature in their mobile phone app. Instaed of scraping the entire web, they will only need to gather their own data. Users' requests will be processed on just the data in the their platform. 
  If the prior approach discussed should be pursued further, one way out will be to use a 3rd party which has curated most of this data or is the source of most of this data(eg Google). The cloud vision API could be used such that when a user posts a picture, we aren't querying our tiny database but instead google's massive database. Another provider who offers a service very similar to Google is Cloudsight. They have spent years curating this type of dataset in house and now offer it as a service.
  
## Insights from Tests

  After running various tests i came to the following conclusions:
    - Use the package bcolz to store the activations extracted from the training data in the vm's disk instead of it's RAM.
    - Inorder to perform indexing and cosine simialrity matching, you can use one of the following:
      - ANnoy from spotify
      - Nmslib
      - ELasticSearch
      I use Nmslib for all my experiments because they were at a small scale. For production, i'll suggest using Elasticsearch or Annoy
      
    - Finetuning a language model on the task as a classification problem woorks well but in most cases curating a well labelled dataset is beyond feasible. Instead it is better to either finetune a model pretrained on imagenet on a small well labelled subsection of the dataset so that the model understands that domain space. However, simply using a pretrained model without any form of finetuning works well and is faster to implement but the above stated method will help to improve the model
    - I'm yet to implement an appropriate metric for this task. WHat i use as a metric now is the cosine similarity score however the recall@N score seems to be the most widely adopted
    - It is important to remember the order of the dataset during embedding extraction (if possible, avoid shuffling). This will help in building a reasonable database with appropriate indexes
      
## Useful resources

 - How shopai does similarity matching https://www.youtube.com/watch?v=NQ9tUtzXHhg
 - https://shopai.co/
 - practical Deep learning book chapter 4
 - Devise paper
 - Jeremy's implementation of Devise
 - visual search at alibaba https://102.alibaba.com/downloadFile.do?file=1534297833520/VisualSearch.pdf
