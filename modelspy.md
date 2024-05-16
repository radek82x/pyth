from django.db import models
from django.contrib.auth.models import User

class Chat(models.Model):
    topic = models.CharField(max_length=255)
    route_id = models.IntegerField()
    created_at = models.DateTimeField(auto_now_add=True)
    last_message_at = models.DateTimeField(null=True, blank=True)
    created_by = models.ForeignKey(User, on_delete=models.CASCADE, related_name='created_chats')
    recipient = models.ForeignKey(User, on_delete=models.CASCADE, related_name='received_chats')

class Message(models.Model):
    content = models.TextField()
    file = models.FileField(upload_to='chat_files/', null=True, blank=True)
    image = models.ImageField(upload_to='chat_images/', null=True, blank=True)
    timestamp = models.DateTimeField(auto_now_add=True)
    is_read = models.BooleanField(default=False)
    read_at = models.DateTimeField(null=True, blank=True)
    chat = models.ForeignKey(Chat, on_delete=models.CASCADE)
    sender = models.ForeignKey(User, on_delete=models.CASCADE, related_name='sent_messages')
    receiver = models.ForeignKey(User, on_delete=models.CASCADE, related_name='received_messages')

class HelpRequest(models.Model):
    timestamp = models.DateTimeField(auto_now_add=True)
    is_handled = models.BooleanField(default=False)
    chat = models.ForeignKey(Chat, on_delete=models.CASCADE)
    handler = models.ForeignKey(User, on_delete=models.CASCADE)
    sender = models.ForeignKey(User, on_delete=models.CASCADE, related_name='sent_help_requests')
    response = models.TextField(null=True, blank=True)

