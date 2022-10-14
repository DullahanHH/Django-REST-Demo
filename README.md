# Django REST Framework Study Note
Doc: https://www.django-rest-framework.org/tutorial/1-serialization/#tutorial-1-serialization

Migration and Database sync recall:
- [python manage.py makemigrations APPNAME]
- [python manage.py migrate APPNAME]


Serializer (Tut-1):
- Provide a way to serializing and deserializing the snippet instances into representations such as JSON.
- In serializer, the create() and update() methods defined the behaviour of serializer.save()
- Using ModelSerializer save times, which automatically determined fields and implement create(), update() methods.
- The two views represent all snippets or specific snippet
  - snippet_list(request) -> Show all snippets (exURL: http://127.0.0.1:8000/snippets/)
  - snippet_detail(request, pk) -> Show specific snippet by pk (exURL: http://127.0.0.1:8000/snippets/2/)


Request and Response (Tut-2):
- Difference between request.data and request.POST:
  - request.POST  # Only handles form data.  Only works for 'POST' method.
  - request.data  # Handles arbitrary data.  Works for 'POST', 'PUT' and 'PATCH' methods.
- Response objects:
  - return Response(data)  # Renders to content type as requested by the client.
- Status code:
  - REST framework contain more status code, like "HTTP_400_BAD_REQUEST".
- Wrappers:
  - @api_view -> function-based views.
  - APIView -> class-based views.
- The Response() object replaced HttpResponse() and JsonResponse()
- "request.data" can handle incoming json requests
- Adding optional format suffixes to URLs
  - Add suffixes to API allow URLs handle like 'http://example.com/api/items/4.json'.
    - http://127.0.0.1:8000/snippets.json  # JSON suffix
    - http://127.0.0.1:8000/snippets.api   # Browsable API suffix
  - Control the request format we send:
    - [http --json POST http://127.0.0.1:8000/snippets/ code="print(456)"]
      - Created a POST in json format


Class-based Views (Tut-3):
- function-based -> class-based
- In snippets/urls.py, [path('snippets/', views.SnippetList),] needs to slightly change to [path('snippets/', views.SnippetList.as_view()),]
- mixin:
  - Use mixin to compose reusable part.
  - GenericAPIView: provide the core functionality.
  - ListModelMixin, CreateModelMixin: provide .list() and .create() actions.
  - RetrieveModelMixin, UpdateModelMixin, DestroyModelMixin: provide .retrieve(), .update() and .destroy() actions.
- mixed-in generic views:
  - More clean code.


Summary:
- Visualized api
- Reusable code, clean code
- Able to switch form (.json, .api, etc)