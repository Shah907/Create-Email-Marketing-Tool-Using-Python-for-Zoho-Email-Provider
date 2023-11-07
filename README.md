# Create-Email-Marketing-Tool-Using-Python-for-Zoho-Email-Provider
Create Email Marketing Tool Using Python for Zoho Email Provider
Source: See Complete Guide here at ('https://deepdecide.com/free-email-marketing-tool-for-wordpress-using-python')

import requests
import smtplib
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText
from email.mime.image import MIMEImage
from email.mime.base import MIMEBase
from email import encoders
import urllib

def send_mail(post_title, post_url, post_excerpt, to_address):
    smtp_server = 'smtp.zoho.com'
    smtp_port = 465
    USERNAME = 'your-email@zoho.com'
    PASSWORD = 'your-password'
    
    from_address = USERNAME

    msg = MIMEMultipart('mixed')
    msg['Subject'] = "New Blog Post Alert!"
    msg['From'] = f"Deepdecide <{from_address}>"  # Sender name is added here
    msg['To'] = to_address

    # Embed the image in the html then use CID to reference the image source
    img_data = urllib.request.urlopen('http://your-image-url.jpg').read()
    image = MIMEImage(img_data, name=os.path.basename('your-image-file-name.jpg'))
    image.add_header('Content-ID', '<{}>'.format('banner'))
    msg.attach(image)

    # Construct email in HTML format
    html = f"""
      <h1>{post_title}</h1>
      <p>{post_excerpt}</p>
      <a href="{post_url}" target="_blank">
        <img src="cid:banner" alt="{post_title}" />
      </a>
      """

    part = MIMEText(html, 'html')
    msg.attach(part)

    server = smtplib.SMTP_SSL(smtp_server,smtp_port)
    server.login(USERNAME, PASSWORD)
    server.sendmail(from_address, to_address, msg.as_string())
    server.quit()

def send_new_post_alert():
    response = requests.get('https://your-wordpress-site.com/wp-json/wp/v2/posts')
    posts = response.json()

    newest_post = posts[0]
    post_title = newest_post.get('title').get('rendered')
    post_url = newest_post.get('link')
    post_excerpt = newest_post.get('excerpt').get('rendered')

    with open('emails.txt', 'r') as f:
        emails = f.read().splitlines()

    for email in emails:
        send_mail(post_title, post_url, post_excerpt, email)

if __name__ == "__main__":
    send_new_post_alert()
