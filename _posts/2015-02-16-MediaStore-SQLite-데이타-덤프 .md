---
layout: post
title: 150216 MediaStore SQLite 데이타 덤프 
comments: true
tags:
- mediastore
- sqlite
- android
- study
- dump
---

<!-- TOC -->

- [소스코드](#소스코드)
- [덤프결과](#덤프결과)

<!-- /TOC -->


<br>
<br>
<br>



MediaStore클래스로 접근 가능한 SQLite테이블 모두를 덤프 떠봤습니다.
접근 가능한 테이블을 모두 18개 이고 그중 유의미한 테이블은 8개 정도이네요
로컬 미디어 관련 개발할때 데이타를 쉽게 찾는데 도움이 되었으면 좋겠네요
 

# 소스코드

```java
private void onClickLocal() {
    Log.i("hhddebug", "---------------------------------------------------------");
    Log.i("hhddebug", "---------------------------------------------------------");
    Log.i("hhddebug", "round : " + round);

    switch (round) {
        case 0:
            printMediaStoreByUri(MediaStore.Audio.Albums.EXTERNAL_CONTENT_URI);
            break;
        case 1:
            printMediaStoreByUri(MediaStore.Audio.Albums.INTERNAL_CONTENT_URI);
            break;
        case 2:
            printMediaStoreByUri(MediaStore.Audio.Artists.EXTERNAL_CONTENT_URI);
            break;
        case 3:
            printMediaStoreByUri(MediaStore.Audio.Artists.INTERNAL_CONTENT_URI);
            break;
        case 4:
            printMediaStoreByUri(MediaStore.Audio.Genres.EXTERNAL_CONTENT_URI);
            break;
        case 5:
            printMediaStoreByUri(MediaStore.Audio.Genres.INTERNAL_CONTENT_URI);
            break;
        case 6:
            printMediaStoreByUri(MediaStore.Audio.Media.EXTERNAL_CONTENT_URI);
            break;
        case 7:
            printMediaStoreByUri(MediaStore.Audio.Media.INTERNAL_CONTENT_URI);
            break;
        case 8:
            printMediaStoreByUri(MediaStore.Audio.Playlists.EXTERNAL_CONTENT_URI);
            break;
        case 9:
            printMediaStoreByUri(MediaStore.Audio.Playlists.INTERNAL_CONTENT_URI);
            break;
        case 10:
            printMediaStoreByUri(MediaStore.Images.Media.EXTERNAL_CONTENT_URI);
            break;
        case 11:
            printMediaStoreByUri(MediaStore.Images.Media.INTERNAL_CONTENT_URI);
            break;
        case 12:
            printMediaStoreByUri(MediaStore.Images.Thumbnails.EXTERNAL_CONTENT_URI);
            break;
        case 13:
            printMediaStoreByUri(MediaStore.Images.Thumbnails.INTERNAL_CONTENT_URI);
            break;
        case 14:
            printMediaStoreByUri(MediaStore.Video.Media.EXTERNAL_CONTENT_URI);
            break;
        case 15:
            printMediaStoreByUri(MediaStore.Video.Media.INTERNAL_CONTENT_URI);
            break;
        case 16:
            printMediaStoreByUri(MediaStore.Video.Thumbnails.EXTERNAL_CONTENT_URI);
            break;
        case 17:
            printMediaStoreByUri(MediaStore.Video.Thumbnails.INTERNAL_CONTENT_URI);
            break;
    }

    round = round + 1;
}

private void printMediaStoreByUri(Uri mediaStoreUri) {
    Log.i("hhddebug", "mediaStoreUri : " + mediaStoreUri);
    Cursor cur = null;

    try {
        ContentResolver cr = getContentResolver();
        cur = cr.query(mediaStoreUri, null, null, null, null);
        int cursorRowCount = cur.getCount();
        Log.i("hhddebug", "cursorRowCount : " + cursorRowCount);

        if (cursorRowCount == 0)
            return;


        int midRow = cursorRowCount / 2;
        Log.i("hhddebug", "midRow : " + midRow);

        cur.moveToPosition(midRow);
        int colCount = cur.getColumnCount();
        Log.i("hhddebug", "colCount : " + colCount);

        for (int col = 0; col < colCount; col++) {
            String columnName = cur.getColumnName(col);
            String value = cur.getString(col);
            Log.i("hhddebug", "" + "col" + col + ":" + columnName + ":" + value);
        }
    } catch (Exception e) {
        Log.i("hhddebug", "e : " + e);
    } finally {
        if (cur != null) {
            cur.close();
        }
    }
}
```

# 덤프결과

```text 
round : 0
mediaStoreUri : content://media/external/audio/albums
cursorRowCount : 137
midRow : 68
colCount : 11
col0:_id:120
col1:album:1집 사랑하기 때문에
col2:album_key:            &"\             & 4       & 4‰       &.4       &
 50627148
col3:album_index:#
col4:minyear:1987
col5:maxyear:1987
col6:artist:유재하
col7:artist_id:107
col8:artist_key:      & V       &"6       &.4
col9:numsongs:2
col10:album_art:/storage/emulated/0/Android/data/com.android.providers.media/albumthumbs/1407518209300
```

```text
round : 1
mediaStoreUri : content://media/internal/audio/albums
cursorRowCount : 1
midRow : 0
colCount : 11
col0:_id:4
col1:album:ui
col2:album_key:      P      8      78638153
col3:album_index:u
col4:minyear:null
col5:maxyear:null
col6:artist:<unknown>
col7:artist_id:2
col8:artist_key:
col9:numsongs:49
col10:album_art:null
```

```text
round : 2
mediaStoreUri : content://media/external/audio/artists
cursorRowCount : 61
midRow : 30
colCount : 6
col0:_id:107
col1:artist:유재하
col2:artist_key:      & V       &"6       &.4
col3:artist_index:ㅇ
col4:number_of_albums:1
col5:number_of_tracks:2
```

```text
round : 3
mediaStoreUri : content://media/internal/audio/artists
cursorRowCount : 1
midRow : 0
colCount : 6
col0:_id:2
col1:artist:<unknown>
col2:artist_key:
col3:artist_index:#
col4:number_of_albums:1
col5:number_of_tracks:49
```

```text
round : 4
mediaStoreUri : content://media/external/audio/genres
cursorRowCount : 3
midRow : 1
colCount : 4
col0:_id:2
col1:name:Other
col2:genre_key:      D      N      6      0      J
col3:genre_index:o
```

```text
round : 5
mediaStoreUri : content://media/internal/audio/genres
e : android.database.sqlite.SQLiteException: no such table: audio_genres (code 1): , while compiling: SELECT * FROM audio_genres
```

```text
round : 6
mediaStoreUri : content://media/external/audio/media
cursorRowCount : 194
midRow : 97
colCount : 44
col0:_id:40952
col1:_data:/storage/emulated/0/NaverMusic/mp3/쿨 - 01 - 해변의 여인.mp3
col2:_display_name:쿨 - 01 - 해변의 여인.mp3
col3:_size:5309743
col4:mime_type:audio/mpeg
col5:date_added:1409243149
col6:is_drm:0
col7:date_modified:1409243148
col8:title:해변의 여인
col9:title_key:      &.6       & @g       & Z             & @       & \g
col10:duration:220056
col11:artist_id:126
col12:composer:null
col13:album_id:184
col14:track:1001
col15:year:1997
col16:is_ringtone:0
col17:is_music:1
col18:is_alarm:0
col19:is_notification:0
col20:is_podcast:0
col21:bookmark:null
col22:album_artist:쿨
col23:protected_type:0
col24:storage_id:65537
col25:is_hifi:0
col26:folder_id:9
col27:date_played:1410149488702
col28:count_played:2
col29:is_favorite:null
col30:index_key:ㅎ
col31:folder_id:1:9
col32:folder_path:/storage/emulated/0/NaverMusic/mp3
col33:folder_key:      @      F            -2055505840
col34:folder:mp3
col35:folder_index:m
col36:artist_id:1:126
col37:artist_key:      &(No
col38:artist:쿨
col39:artist_index:ㅋ
col40:album_id:1:184
col41:album_key:                  &"\             L      P      @      @      0      J            L      N      D      J      X      53224
col42:album:3.5집 Summer Story
col43:album_index:#
```

```text
round : 7
mediaStoreUri : content://media/internal/audio/media
cursorRowCount : 116
midRow : 58
colCount : 44
col0:_id:102
col1:_data:/system/media/audio/ringtones/14_Guitar_Trip.ogg
col2:_display_name:14_Guitar_Trip.ogg
col3:_size:718700
col4:mime_type:application/ogg
col5:date_added:86526
col6:is_drm:0
col7:date_modified:1392382054
col8:title:기타와 함께
col9:title_key:      &
 
col10:duration:28548
col11:artist_id:1
col12:composer:null
col13:album_id:3
col14:track:0
col15:year:null
col16:is_ringtone:1
col17:is_music:0
col18:is_alarm:0
col19:is_notification:0
col20:is_podcast:0
col21:bookmark:null
col22:album_artist:null
col23:protected_type:0
col24:storage_id:65537
col25:is_hifi:0
col26:folder_id:3
col27:date_played:null
col28:count_played:null
col29:is_favorite:null
col30:index_key:ㄱ
col31:folder_id:1:3
col32:folder_path:/system/media/audio/ringtones
col33:folder_key:      J      8      B      4      N      D      B      0      L      293500348
col34:folder:ringtones
col35:folder_index:r
col36:artist_id:1:1
col37:artist_key:      >      4            0      >      0      ,      N      J      D      B      8      ,      L
col38:artist:LG Electronics
col39:artist_index:l
col40:album_id:1:3
col41:album_key:      >      4            @      D      *      8      >      0      293500348
col42:album:LG Mobile
col43:album_index:l
```

```text
round : 8
mediaStoreUri : content://media/external/audio/playlists
cursorRowCount : 0
```

```text
round : 9
mediaStoreUri : content://media/internal/audio/playlists
e : android.database.sqlite.SQLiteException: no such table: audio_playlists (code 1): , while compiling: SELECT * FROM audio_playlists
```

```text
round : 10
mediaStoreUri : content://media/external/images/media
cursorRowCount : 1281
midRow : 640
colCount : 25
col0:_id:33833
col1:_data:/storage/emulated/0/DCIM/Camera/20140730_203607.jpg
col2:_size:2670233
col3:_display_name:20140730_203607.jpg
col4:mime_type:image/jpeg
col5:title:20140730_203607
col6:date_added:1406745367
col7:date_modified:1406745367
col8:description:null
col9:picasa_id:null
col10:isprivate:null
col11:latitude:null
col12:longitude:null
col13:datetaken:1406745367138
col14:orientation:0
col15:mini_thumb_magic:8323016053770083978
col16:bucket_id:-1739773001
col17:bucket_display_name:Camera
col18:width:4160
col19:height:2340
col20:protected_type:0
col21:storage_id:65537
col22:is_favorite:null
col23:is_lock:0
col24:burst_id:20140730_203607.jpg
```

```text
round : 11
mediaStoreUri : content://media/internal/images/media
cursorRowCount : 0
```

```text
round : 12
mediaStoreUri : content://media/external/images/thumbnails
cursorRowCount : 1037
midRow : 518
colCount : 7
col0:_id:643
col1:_data:/storage/emulated/0/DCIM/.thumbnails/1406742272681.jpg
col2:image_id:33672
col3:kind:1
col4:width:520
col5:height:293
col6:storage_id:65537
```

```text
round : 13
mediaStoreUri : content://media/internal/images/thumbnails
cursorRowCount : 0
```

```text
round : 14
mediaStoreUri : content://media/external/video/media
cursorRowCount : 9
midRow : 4
colCount : 36
col0:_id:52009
col1:_data:/storage/emulated/0/DCIM/Camera/20141016_140627.mp4
col2:_display_name:20141016_140627.mp4
col3:_size:16319857
col4:mime_type:video/mp4
col5:date_added:1413435997
col6:date_modified:1413435997
col7:title:20141016_140627
col8:duration:6985
col9:artist:null
col10:album:null
col11:resolution:1920x1080
col12:description:null
col13:isprivate:null
col14:tags:null
col15:category:null
col16:language:null
col17:mini_thumb_data:null
col18:latitude:null
col19:longitude:null
col20:datetaken:1413435997672
col21:mini_thumb_magic:-4401734050442370521
col22:bucket_id:-1739773001
col23:bucket_display_name:Camera
col24:bookmark:null
col25:width:null
col26:height:null
col27:protected_type:0
col28:storage_id:65537
col29:is_favorite:null
col30:date_played:null
col31:is_lock:0
col32:title_key:                                                      B
col33:index_key:#
col34:video_filetype:MP4
col35:video_iswatched:null
```

```text
round : 15
mediaStoreUri : content://media/internal/video/media
cursorRowCount : 0
```

```text
round : 16
mediaStoreUri : content://media/external/video/thumbnails
cursorRowCount : 32
midRow : 16
colCount : 7
col0:_id:17
col1:_data:/storage/emulated/0/DCIM/.thumbnails/1413438125327.jpg
col2:video_id:52059
col3:kind:1
col4:width:288
col5:height:512
col6:storage_id:65537
```

```text
round : 17
mediaStoreUri : content://media/internal/video/thumbnails
cursorRowCount : 0
```