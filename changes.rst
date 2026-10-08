v6.1.1 (20261008)
=================

* Windows: fix a crash (exit code 0xC0000409) when the command line
  version is started by a program without a console, such as a GUI
  program. Only streams without a handle are reopened on the parent's
  console now, so output redirected to a pipe or a file stays there and
  the caller receives the progress messages.

* Windows: the command line version no longer creates a QApplication,
  like on Linux and macOS. It works without a Qt platform plugin and
  can't show the "no Qt platform plugin" error dialog. The arguments are
  read as Unicode from the Windows command line.



v6.1 (20261006)
===============

* Fix the d2v files of MPEG-1 system streams in which a keyframe starts
  in the middle of a PES packet. ffmpeg gives no position for such a
  keyframe, so the d2v got a line with the position -1 and d2vsource
  could not seek to the frames behind it ("Seek pattern broke
  d2vsource!"). The check of the keyframe locations now treats these
  lines as unreachable and merges them into the previous line, like
  other unreachable keyframes. Decoding a whole file is not affected.

* Automatic builds for Windows x64, Linux x86_64 and macOS arm64 with
  GitHub Actions. Pushing a tag ``v*`` creates a GitHub release with
  these builds.



v6 (20260917)
=============

* Require Qt 6, FFmpeg 7.0 or newer and VapourSynth R55 or newer (API 4).
  Qt 5, older FFmpeg versions and VapourSynth API 3 are no longer
  supported.

* Remove the autotools build system. Meson is the only build system now.
  If VapourSynth's pkg-config file is not available, the location of
  VapourSynth4.h can be passed with ``-Dvapoursynth_includedir``.

* The command line interface no longer creates a QApplication, so it
  works without a display or a Qt platform plugin. The graphical
  interface is still shown if there are no command line parameters or
  if they are all recognised by Qt.

* Fix the frame rate in d2v files: use the frame rate from the sequence
  header. With FFmpeg 7 and newer, MPEG elementary streams always got
  25 fps. With other containers the frame rate was derived from the
  timestamps and could be something like 179/6 instead of 30000/1001.

* Fix crash when demuxing audio from PVA files, where the video and the
  audio stream have the same id. The check of the keyframe positions
  could also mistake an audio packet for a video packet in such files.

* Put the real bit rate into the names of demuxed audio files again. It
  was always 0 since the FFmpeg 6 update.

* Fix memory leaks: the input buffer was not freed when closing a file,
  and Wave64 audio files were not closed properly.

* The preview uses VapourSynth API 4. On macOS it first looks for the
  VapourSynth library in ``d2vwitch.app/Contents/Frameworks/vapoursynth``.
  The environment variable ``D2VWITCH_VAPOURSYNTH_LIB`` can point to a
  different VapourSynth library.

* Remove the donation link from the status bar.



v5 (20201112)
=============

* Fix field order detection of frames coded as fields. The field order
  of frames coded as two field pictures was always detected as bottom
  field first. The top_field_first bit is actually not applicable to
  field pictures. Instead, use the order in which the field pictures
  appear in the stream. Bug present since v1.



v4 (20200610)
=============

* Fix crash when demuxing the video using the graphical interface.

* Fix bad d2v output when calculating the audio delays. This bug
  probably affected anyone who used the graphical interface whether
  demuxing audio tracks or not, plus anyone who used the command line
  interface to demux audio tracks. The result was d2v files with the
  wrong number of frames and possibly visible decoding errors. This
  bug was introduced in v3.
  
* If d2vsource.dll is not found in VapourSynth's autoload locations,
  try to load it from PATH and the location of ``d2vwitch.exe``. This
  is for Windows only.



v3 (20190404)
=============

* Fix incorrectly reported codec type ("unknown") with some newer
  ffmpeg versions. This may break compilation with older ffmpeg
  versions.

* Hopefully fix the freezing of the graphical interface in Windows
  when clicking "Add files" or "Browse", by using native file
  selectors.

* Avoid leaving behind empty files when the audio decoders can't be
  opened.

* Skip undecodable leading (in coded order) non-keyframes. It helps
  produce more correct d2v files with imperfect streams.

* Skip unknown picture types. They probably can't be decoded.

* Fix incorrect flagging of some MPEG2 closed GOPs as open.

* Fix incorrect flagging of leading B frames in closed GOPs in MPEG2
  streams.

* Avoid "Could not connect to display" error when running without X.

* Don't ignore the ``--video-id`` parameter.

* Avoid the long delay when adding or removing files experienced by
  some Windows users with NTFS filesystems. The downside is that the
  D2V file name box won't necessarily turn red when the selected
  folder is not writable.

* Don't make the D2V file name box red when there are no video files.

* Fix an unlikely memory leak in the LPCM demuxing code.

* Fix a small memory leak that was always present.

* If the colormatrix is undefined, guess it from the resolution.

* Use backslashes instead of forward slashes in file names in Windows.

* Include the pixel format in the output of ``--info``.

* Write ``input.d2v`` instead of ``input.vob.d2v``,
  ``input T80 blah.ac3`` instead of ``input.vob.d2v T80 blah.ac3``,
  ``input.1000-2000.m2v`` instead of ``input.d2v.1000-2000.m2v``,
  ``input.1000-2000.d2v`` instead of ``input.d2v.1000-2000.m2v.d2v``.

* Make the graphical interface warn before overwriting any files.

* Make the command line interface automatically index files in
  ``vts_xx_y.vob`` sequences. The graphical interface doesn't do this
  because it's much easier to select all the relevant files there.

* Add option ``--single-input`` to disable the automatic indexing of
  ``vts_xx_y.vob`` sequences.

* Calculate the delay required by each demuxed audio track and write
  it into the audio file name. As part of this feature, the audio
  tracks are no longer demuxed from the beginning of the file, but
  from the first video keyframe onwards. This seems to be what DGIndex
  does.

* Add option ``--ffmpeg-log-level``.

* Add option ``--relative-paths`` and a corresponding check box in the
  graphical interface, to write relative paths in d2v files.

* Write ``d2vwitch.ini`` next to ``d2vwitch.exe`` in order to remember
  some settings, like the one about relative paths. (Technically it's
  ``<executable name>.ini``.)

* Make the graphical interface remember the size and position of the
  main window.



v2 (20161209)
=============

* Rename the executable from ``D2VWitch`` to ``d2vwitch`` because no
  one likes typing commands with capital letters in them.

* Remux LPCM audio into Wave 64 instead of producing unusable audio
  files.

* Guess the channel layout for LPCM audio on DVDs based on the number
  of channels, instead of saying it has zero channels.

* Fix occasional crash when demuxing LPCM audio.

* Give audio files proper extensions instead of ``.audio``.

* Add option ``--input-range``, which sets the YUVRGB_Scale field
  according to the video's colour range. Make it default to limited
  range.

* Add a graphical interface, launched when no command line parameters
  are supplied.



v1 (20160116)
=============

* Initial release.
