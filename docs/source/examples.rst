Examples
========

.. toctree::

``instaLooter`` also provides an :abbr:`API (Application Programmable Interface)`
that can be used to extend the capabilities of ``instaLooter``, to fit your
needs more tightly or to integrate ``instaLooter`` to your program.


Store the links to the pictures and videos of a profile into a file
-------------------------------------------------------------------

.. code::

   from instaLooter import InstaLooter
   looter = InstaLooter(profile="targetprofile")

   with open("outputfile.txt", "w") as output:
       for media in looter.medias():
           if media['is_video']:
               url = looter.get_post_info(media['code'])['video_url']
           else:
               url = media['display_src']
           output.write("{}\n".format(url))


Get a set of users that commented a given profile
-------------------------------------------------

.. code::

  from instaLooter import InstaLooter
  looter = InstaLooter(profile="targetprofile")

  users = set()
  for media in looter.medias(with_pbar=True):
     post_info = looter.get_post_info(media['code'])
     for comment in post_info['edge_media_to_comment']['edges']:
         user = comment['node']['owner']['username']
         users.add(user)


Get a set of users tagged in pictures of a given profile
--------------------------------------------------------

.. code::

   from instaLooter import InstaLooter
   looter = InstaLooter(profile="targetprofile")

   users = set()
   for media in looter.medias(with_pbar=True):
       post_info = looter.get_post_info(media['code'])
       for usertag in post_info['edge_media_to_tagged_user']['edges']:
           user = usertag['node']['user']['username']
           users.add(user)


Use Instagram to download resized pictures
-----------------------------------------------

Downloaded pictures will all be resized by IG to be 320x320 pixels
before being downloaded.

.. code::

    import re # the regex module
    from instaLooter import InstaLooter


    def resizer(media):
        cleaning_regex = re.compile(r"(s[0-9x]*/)?(e[0-9]*)/")
        return cleaning_regex.sub('s320x320/', media['display_src'])

    looter = InstaLooter(profile="xxxx", get_videos=True, url_generator=resizer)
    looter.download()
