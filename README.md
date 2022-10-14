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


Authentication & Permissions (Tut-4):
- Add user serializer to serializer.py
- Users are connected to snippets, one user could have multiple snippets.
- Since we want users access the view as read-only, so the generic user views are ListAPIView and RetrieveAPIView.
- Add to urls.py
  - path('users/', views.UserList.as_view()),     # show all users
  - path('users/<int:pk>/', views.UserDetail.as_view()),    # show specific user by pk
- Associating Snippets with Users:
  - Override .create(), let the owner value equals to the user how create the snippet:
  - def perform_create(self, serializer):
  -       serializer.save(owner=self.request.user)
  - In serializer.py, update SnippetSerializer:
    - owner = serializers.ReadOnlyField(source='owner.username')
    - And remember to add 'owner' to Meta class
  - Add [permission_classes = [permissions.IsAuthenticatedOrReadOnly]] to both the SnippetList and SnippetDetail view classes.
    - After adding this line, user no longer can edit or create snippets.
- Add login view:
  - urlpatterns += [
    path('api-auth/', include('rest_framework.urls')),
  ]
  - Above code add to core url.py, a login button will display in the top right corner.
  - the 'api-auth/' can be customized to other url.
- To ensure everyone can see the snippets and only the snippet creator can update or delete, a custom permission is necessary.
  - Create snippets/permissions.py
  - [return obj.owner == request.user]   # Write permissions are only allowed to the owner of the snippet.
  - Add custom permission to SnippetDetail:
    - permission_classes = [permissions.IsAuthenticatedOrReadOnly, IsOwnerOrReadOnly]


Relationships & Hyperlinked APIs (Tut-5):
- Hyperlinked relationship: ModelSerializer -> HyperlinkedModelSerializer
-The HyperlinkedModelSerializer has the following differences from ModelSerializer:
  - It does not include the id field by default.
  - It includes an url field, using HyperlinkedIdentityField.
  - Relationships use HyperlinkedRelatedField, instead of PrimaryKeyRelatedField.
- [highlight = serializers.HyperlinkedIdentityField(view_name='snippet-highlight', format='html')]
  - In serializers.py, the above line has format='html', which indicate the url must return 'html' suffix, not 'json'
- URL naming:
  - Add name to each url in snippets/urls.py
- Adding pagination:
  - In tutorial/settings.py, add following:
  - REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 10
    }


ViewSets & Routers (Tut-6):
- ViewSets could reduce the amount of code
  - ex: UserList, UserDetail -> UserViewSet
  - ex: SnippetList, SnippetDetail, SnippetHighlight -> SnippetViewSet
- The '@action' decorator is used to create custom action other than the viewset provided (create, list, update, etc)
- Routers:
  - Don't need to design the URL conf ourselves.
  - The conventions for wiring up resources into views and urls can be handled automatically.
  - The DefaultRouter class contain the API root view.


Summary:
- Visualized api
- Reusable code, clean code
- Able to switch form (.json, .api, etc)