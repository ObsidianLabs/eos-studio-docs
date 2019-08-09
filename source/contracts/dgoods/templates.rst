Metadata Templates
===========================================

.. cpp:namespace:: dgoods

In order for wallets or dApps to support various digital goods, 
there need to be standards associated with the metadata. 
Therefore, the dGoods standard also defines templates based on the type of good. 
The template data should be returned by the URI 
``base_uri`` (from table :cpp:var:`dgoodstats`) attached with
``relative_uri`` (from table :cpp:var:`dgood`).

The following templates are candidates dGoods has put forth, but this 
is to be a collaborative exercise. The dGoods standard also provides 
a `repository of templates <https://github.com/MythicalGames>`_ 
that are agreed upon by the community. 
All metadata is formatted as JSON objects specified from the template types.


2dgameAsset
-------------------------------------------

========================  ============  ============================================================
Property                  Type
========================  ============  ============================================================
type                      string        ``"2dgameAsset"``
name                      string        identifies the asset the token represents
description               string        short description of the asset the token represents
imageSmall                string        URI pointing to image resource size 150 x 150
imageLarge                string        URI pointing to image resource size 1024 x 1024
details                   object        Key value pairs to render in a detail view, i.e. 
                                        ``{"strength": 5}``
authenticityImage         string        URI pointing to resource with mime type image 
                                        representing certificate of authenticity 
========================  ============  ============================================================


3dgameAsset
-------------------------------------------

========================  ============  ============================================================
Property                  Type
========================  ============  ============================================================
type                      string        ``"3dgameAsset"``
name                      string        identifies the asset the token represents
description               string        short description of the asset the token represents
imageSmall                string        URI pointing to image resource size 150 x 150
imageLarge                string        URI pointing to image resource size 1024 x 1024
3drender                  string        URI pointing to js webgl for rendering 3d object
details                   object        Key value pairs to render in a detail view, i.e. 
                                        ``{"strength": 5}``
authenticityImage         string        URI pointing to resource with mime type image 
                                        representing certificate of authenticity 
========================  ============  ============================================================


ticket
-------------------------------------------

========================  ============  ============================================================
Property                  Type
========================  ============  ============================================================
type                      string        ``"ticket"``
name                      string        identifies the event the ticket is for
description               string        short description of the event the ticket is for
location                  string        name of the location where the event is being held
address                   string        address of the location where the event is being held
date                      time          starting date of the event
time                      time          starting time of the event
imageSmall                string        URI pointing to image resource size 150 x 150
imageLarge                string        URI pointing to image resource size 1024 x 1024
details                   object        Key value pairs to render in a detail view, i.e. 
                                        ``{"openingAct": "Nickelback"}``
authenticityImage         string        URI pointing to resource with mime type image 
                                        representing certificate of authenticity 
duration                  string        length of time the event lasts for 
row                       string        row where seat is located 
seat                      string        seat number or GA for General Admission 
========================  ============  ============================================================


art
-------------------------------------------

========================  ============  ============================================================
Property                  Type
========================  ============  ============================================================
type                      string        ``"art"``
name                      string        identifies the asset the token represents
description               string        short description of the asset the token represents
creator                   string        creator(s) of the art
imageSmall                string        URI pointing to image resource size 150 x 150
imageLarge                string        URI pointing to image resource size 1024 x 1024
date                      time          date artwork was created
details                   object        Key value pairs to render in a detail view, i.e. 
                                        ``{"materials": ["oil", "wood"], "location": "SFMOMA"}``
authenticityImage         string        URI pointing to resource with mime type image 
                                        representing certificate of authenticity 
========================  ============  ============================================================


jewelry
-------------------------------------------

========================  ============  ============================================================
Property                  Type
========================  ============  ============================================================
type                      string        ``"jewelry"``
name                      string        identifies the asset the token represents
description               string        short description of the asset the token represents
imageSmall                string        URI pointing to image resource size 150 x 150
imageLarge                string        URI pointing to image resource size 1024 x 1024
image360                  string        URI pointing to image resource for 360 image
manufactureDate           time          date the jewelry was manufactured
manufacturePlace          string        place the jewelry was manufactured
details                   object        Key value pairs to render in a detail view, i.e. 
                                        ``{"caret": 1.22}``
authenticityImage         string        URI pointing to resource with mime type image 
                                        representing certificate of authenticity 
========================  ============  ============================================================
