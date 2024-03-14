# Essential App Case Study



## Classifieds Feature Specs

### Story: Customer requests to see the classifieds

### Narrative #1

```
As an online customer
I want the app to automatically load my latest classifieds
So I can always see the newest classifieds
```

#### Scenarios (Acceptance criteria)

```
Given the customer has connectivity
 When the customer requests to see the classifieds
 Then the app should display the latest classifieds from remote
  And replace the cache with the new classifieds
```

### Narrative #2

```
As an offline customer
I want the app to show the latest saved version of the classifieds
So I can always see the classifieds
```

#### Scenarios (Acceptance criteria)

```
Given the customer doesn't have connectivity
  And there’s a cached version of the classifieds
  And the cache is less than seven days old
 When the customer requests to see the classifieds
 Then the app should display the latest classifieds saved

Given the customer doesn't have connectivity
  And there’s a cached version of the classifieds
  And the cache is seven days old or more
 When the customer requests to see the classifieds
 Then the app should display an error message

Given the customer doesn't have connectivity
  And the cache is empty
 When the customer requests to see the classifieds
 Then the app should display an error message
```

## Use Cases

### Load Classifieds From Remote Use Case

#### Data:
- URL

#### Primary course (happy path):
1. Execute "Load Classifieds" command with above data.
2. System downloads data from the URL.
3. System validates downloaded data.
4. System creates classifieds from valid data.
5. System delivers classifieds.

#### Invalid data – error course (sad path):
1. System delivers invalid data error.

#### No connectivity – error course (sad path):
1. System delivers connectivity error.

---

### Load Classifieds Image Data From Remote Use Case

#### Data:
- URL

#### Primary course (happy path):
1. Execute "Load Image Data" command with above data.
2. System downloads data from the URL.
3. System validates downloaded data.
4. System delivers image data.

#### Cancel course:
1. System does not deliver image data nor error.

#### Invalid data – error course (sad path):
1. System delivers invalid data error.

#### No connectivity – error course (sad path):
1. System delivers connectivity error.

---

### Load Classifieds From Cache Use Case

#### Primary course:
1. Execute "Load Classifieds" command with above data.
2. System retrieves classifieds data from cache.
3. System validates cache is less than seven days old.
4. System creates classifieds from cached data.
5. System delivers classifieds.

#### Retrieval error course (sad path):
1. System delivers error.

#### Expired cache course (sad path): 
1. System delivers no classifieds.

#### Empty cache course (sad path): 
1. System delivers no classifieds.

---

### Load Classifieds. Image Data From Cache Use Case

#### Data:
- URL

#### Primary course (happy path):
1. Execute "Load Image Data" command with above data.
2. System retrieves data from the cache.
3. System delivers cached image data.

#### Cancel course:
1. System does not deliver image data nor error.

#### Retrieval error course (sad path):
1. System delivers error.

#### Empty cache course (sad path):
1. System delivers not found error.

---

### Validate Classifieds Cache Use Case

#### Primary course:
1. Execute "Validate Cache" command with above data.
2. System retrieves feed data from cache.
3. System validates cache is less than seven days old.

#### Retrieval error course (sad path):
1. System deletes cache.

#### Expired cache course (sad path): 
1. System deletes cache.

---

### Cache Classifieds Use Case

#### Data:
- Image Classifieds

#### Primary course (happy path):
1. Execute "Save Classifieds" command with above data.
2. System deletes old cache data.
3. System encodes classifieds.
4. System timestamps the new cache.
5. System saves new cache data.
6. System delivers success message.

#### Deleting error course (sad path):
1. System delivers error.

#### Saving error course (sad path):
1. System delivers error.

---

### Cache Feed Image Data Use Case

#### Data:
- Image Data

#### Primary course (happy path):
1. Execute "Save Image Data" command with above data.
2. System caches image data.
3. System delivers success message.

#### Saving error course (sad path):
1. System delivers error.

---

## Flowchart

![Flowchart drawio](https://github.com/afsalkp007/classifieds-case-study/assets/23147303/23ca56b0-36f5-4c08-b73f-447df5026cee)


## Model Specs

### Classified

| Property               | Type                |
|------------------------|---------------------|
| `created_at`           | `Date`              |
| `price`                | `String`            |
| `name`                 | `String`            |
| `uid`                  | `UUID`              |
| `image_ids`            | `[UUID]`            |
| `image_urls`           | `[URL]`             |
| `image_urls_thumbnails`| `[URL]`             |

### Payload contract

```
GET /classifieds

200 RESPONSE

{
  "results": [
    {
      "created_at": "2019-02-24 04:04:17.566515",
      "price": "AED 5",
      "name": "Notebook",
      "uid": "4878bf592579410fba52941d00b62f94",
      "image_ids": [
        "9355183956e3445e89735d877b798689"
      ],
      "image_urls": [
        "https://demo-app-photos-45687895456123.s3.amazonaws.com/9355183956e3445e89735d877b798689?AWSAccessKeyId=ASIASV3YI6A4RKRFB4X5&;amp;Signature=oL6hIiS34RbY54u0rSDtkjOvwOU%3D&amp;x-amz-security-token=IQoJb3JpZ2luX2VjEGAaCXVzLWVhc3QtMSJHMEUCIQDfSDAE1Q0JmOBIfSEdCzE%2BPIahzqLMYF%2BfgkhOcQfFfAIgVvq5E9RFgufd%2Fy01NQdg1vRKnt9Yv63SULdflUuBJAMq8gIIaRAFGgwxODQzOTg5NjY4NDEiDMKUl4tHBLbob1V9zSrPAqMKVUzAdHaQtKKHpnKe8iGYt14NVaqeTVbR5gAvYykmwcp4UnisCSIn36DB%2ByGe9StKR48EYgJUVVi6ij%2Fe2mtD1Mbp7zEiNYVWHc49KjH3YzJOfpPC04ym7R5rInWfu3ypRvYeyfkdD4TtURmGcHZnJ5EtrNRTe42m9%2BL439bRJUj%2Faug6%2FF1kIZNWKapLXQaimYXfK%2FLnbgCmeCvzAJXLeVRXFzk7zuTYf2lbxP%2FJ13l0%2FAkelMoek%2BUvY5z6w%2Fj2NIpO1QNtdg2yS7UbmTwjQ8lAeSJkNvIrMkyTHW07SBbTcTczFP9U67qt6WxG31aFcoHUM0tXvHSYpBFn3liEfGrX3%2F9nbXgensk6Mr6oHcHpkGu9VJNQ5z9azC4TNRlv382EKMUWPyMHWGYNkPgnphGUFFwrRnj3%2FRN8r9s%2FBxLH6xOWVNc%2BTQVw17MFMNv1yK8GOp4Bitng7SFNer6PH7WG6ApUwYTvo2avl1mp7EGMt50WJ5n0GwP7doF8rARVLmsKDyScNHdC%2BsF4Yz4qVkoUdHcc%2F1hRHuYBGw%2Fk6TCMo7DqqdlVgox%2Fi8A2Zo1TY2BwDBd5d1ihsOmg5HmHqWh6EKwd7oKAaI7KcupYaadRuxdgdlJOqQqEcotRaEy1JUaUiAJiuGr8x5m%2BF%2BlXTj6pgb4%3D&amp;Expires=1710377199"
      ],
      "image_urls_thumbnails": [
        "https://demo-app-photos-45687895456123.s3.amazonaws.com/9355183956e3445e89735d877b798689-thumbnail?AWSAccessKeyId=ASIASV3YI6A4RKRFB4X5&;amp;Signature=TASo7%2BUOaXK0%2FenRfA3j%2F9hHbl4%3D&amp;x-amz-security-token=IQoJb3JpZ2luX2VjEGAaCXVzLWVhc3QtMSJHMEUCIQDfSDAE1Q0JmOBIfSEdCzE%2BPIahzqLMYF%2BfgkhOcQfFfAIgVvq5E9RFgufd%2Fy01NQdg1vRKnt9Yv63SULdflUuBJAMq8gIIaRAFGgwxODQzOTg5NjY4NDEiDMKUl4tHBLbob1V9zSrPAqMKVUzAdHaQtKKHpnKe8iGYt14NVaqeTVbR5gAvYykmwcp4UnisCSIn36DB%2ByGe9StKR48EYgJUVVi6ij%2Fe2mtD1Mbp7zEiNYVWHc49KjH3YzJOfpPC04ym7R5rInWfu3ypRvYeyfkdD4TtURmGcHZnJ5EtrNRTe42m9%2BL439bRJUj%2Faug6%2FF1kIZNWKapLXQaimYXfK%2FLnbgCmeCvzAJXLeVRXFzk7zuTYf2lbxP%2FJ13l0%2FAkelMoek%2BUvY5z6w%2Fj2NIpO1QNtdg2yS7UbmTwjQ8lAeSJkNvIrMkyTHW07SBbTcTczFP9U67qt6WxG31aFcoHUM0tXvHSYpBFn3liEfGrX3%2F9nbXgensk6Mr6oHcHpkGu9VJNQ5z9azC4TNRlv382EKMUWPyMHWGYNkPgnphGUFFwrRnj3%2FRN8r9s%2FBxLH6xOWVNc%2BTQVw17MFMNv1yK8GOp4Bitng7SFNer6PH7WG6ApUwYTvo2avl1mp7EGMt50WJ5n0GwP7doF8rARVLmsKDyScNHdC%2BsF4Yz4qVkoUdHcc%2F1hRHuYBGw%2Fk6TCMo7DqqdlVgox%2Fi8A2Zo1TY2BwDBd5d1ihsOmg5HmHqWh6EKwd7oKAaI7KcupYaadRuxdgdlJOqQqEcotRaEy1JUaUiAJiuGr8x5m%2BF%2BlXTj6pgb4%3D&amp;Expires=1710377199"
      ]
    }
    ...
  ]
}
```

---

## App Architecture
![Architecture drawio](https://github.com/afsalkp007/classifieds-case-study/assets/23147303/672a0c7f-f4e6-4f48-8fa3-75227b598fb9)

