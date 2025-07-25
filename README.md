# 📺 YouTube Companion Dashboard 
This is a mini-dashboard that integrates with the YouTube Data API to help users manage their uploaded videos.

🔗 **Frontend deployemnet link:** https://sonu-companion-dashboard-youtube-frontend.vercel.app  
🔗 **Backend deployemnet link:** https://sonu-companion-dashboard-youtube-backend.vercel.app

---

## 🚀 Features

- Fetch video details using YouTube API
- Add comments and replies to your video
- Delete your comments and replies
- Edit video title and description
- Take private notes for improving your video (saved in MongoDB)
- All user actions are logged into a database
- Google OAuth authentication (with YouTube Data API v3 access)

---

## 🧩 API Endpoints

### 🔍 `GET https://companion-dashboard-youtube.vercel.app/api/video?videoId=${videoId}`
- **Description:** Fetch video details from YouTube API
- **Query Param:** `videoId` – ID extracted from YouTube URL
- **Headers:** `Authorization: Bearer <access_token>`

### 🔍 `PUT  https://companion-dashboard-youtube.vercel.app/api/video/update/titledescription`
- **Description:** Updates the title and description of a YouTube video.
- **Headers:** Authorization: Bearer <access_token>
              Content-Type: application/json
- **Body:** {
              "videoId": "YOUR_VIDEO_ID",
               "title": "New Title",
               "description": "Updated video description"
            }

### 🔍 `POST  https://companion-dashboard-youtube.vercel.app/api/video/comment`
- **Description:** Posts a new comment to the specified YouTube video.
- **Headers:** Authorization: Bearer <access_token>
               Content-Type: application/json
- **Body:** {
              "videoId": "YOUR_VIDEO_ID",
               "text": "This is my comment"
            }

### 🔍 `POST  https://companion-dashboard-youtube.vercel.app//api/video/reply`
- **Description:** Posts a reply to an existing comment on the video.
- **Headers:** Authorization: Bearer <access_token>
Content-Type: application/json
- **Body:** {
  "commentId": "PARENT_COMMENT_ID",
  "text": "This is a reply to the comment"
}

### 🔍 `DELETE  https://companion-dashboard-youtube.vercel.app/api/video/delete/comment?commentId=${commentId}`
- **Description:** Deletes a comment from the video using the comment ID.
- **Headers:** Authorization: Bearer <access_token>
- **Query Param:** commentId: The ID of the comment to be deleted.

### 🔍 `DELETE  https://companion-dashboard-youtube.vercel.app/api/video/delete/reply?replyId=${replyId}`
- **Description:** Deletes a reply to a comment using the reply ID.
- **Headers:** Authorization: Bearer <access_token>
- **Query Param:** replyId: The ID of the reply to be deleted.

### 🔍 `POST  https://companion-dashboard-youtube.vercel.app/api/notes`
- **Description:** Add notes for a video.
- **Headers:** "Content-Type": "application/json",
- **Body:** { videoId: videoData.id, notes }

### Important points for event logs
For every API call like deleting a comment, replying to a comment, posting a comment, or updating the video title and description, I am storing an event log in the database. This helps keep track of all user actions.


# Database Schema 

* logs.model.js
---------------
```
import mongoose, { Schema } from "mongoose";

const logSchema = new Schema(
  {
    videoId: {
      type: String,
      required: true,
    },
    action: {
      type: String,
      enum: [
        "COMMENT_ADDED",
        "REPLY_ADDED",
        "COMMENT_DELETED",
        "REPLY_DELETED",
        "TITLE_AND_DESC_UPDATED",
        "NOTE_ADDED",
        "NOTE_DELETED",
        "NOTE_UPDATED",
      ],
      required: true,
    },
    details: {
      type: Object, 
      default: {},
    },
  },
  {
    timestamps: true,
  }
);

export const Log = mongoose.model("Log", logSchema);
```

* notes.model.js
----------------
```
import mongoose, { Schema } from "mongoose";

const noteSchema = new Schema(
  {
    videoId: {
      type: String,
      required: true,
    },
    note: {
      type: String,
      required: true,
    },
  },
  {
    timestamps: true,
  }
);

export const Note = mongoose.model("Note", noteSchema);
```