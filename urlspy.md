from django.urls import path
from . import views

urlpatterns = [
    path('chats/', views.chat_list, name='chat-list'),
    path('chats/<int:chat_id>/messages/', views.message_list, name='message-list'),
    path('help/', views.help_request, name='help-request'),
]
