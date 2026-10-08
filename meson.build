project('d2vwitch', 'cpp',
        version: '6.1.1',
        default_options: ['buildtype=release', 'cpp_std=c++17'],
        meson_version: '>=0.57')

cxx = meson.get_compiler('cpp')

qt = import('qt6')
# main: links Qt6EntryPoint on Windows, which provides WinMain for the GUI subsystem.
dep_qt = dependency('qt6', modules: ['Core', 'Gui', 'Widgets'], main: true, required: true)

# Only the headers are needed, the library is loaded at runtime.
vs_includedir = get_option('vapoursynth_includedir')
if vs_includedir != ''
  dep_vapoursynth = declare_dependency(include_directories: include_directories(vs_includedir))
else
  dep_vapoursynth = dependency('vapoursynth', version: '>=55').partial_dependency(includes: true, compile_args: true)
endif

if not cxx.has_header('VapourSynth4.h', dependencies: dep_vapoursynth)
  error('VapourSynth4.h not found. Install VapourSynth R55 or later or set -Dvapoursynth_includedir.')
endif

deps = [
  dep_vapoursynth,
  dependency('libavcodec', version: '>=61'),
  dependency('libavformat', version: '>=61'),
  dependency('libavutil', version: '>=59'),
]

moc_headers = [
  'src/GUIWindow.h',
  'src/ListWidget.h',
  'src/ScrollArea.h'
]

processed_files = qt.preprocess(moc_headers: moc_headers)

sources = [
  'src/Audio.cpp',
  'src/Audio.h',
  'src/Bullshit.cpp',
  'src/Bullshit.h',
  'src/D2V.cpp',
  'src/D2V.h',
  'src/D2VWitch.cpp',
  'src/FakeFile.cpp',
  'src/FakeFile.h',
  'src/FFMPEG.cpp',
  'src/FFMPEG.h',
  'src/GUIWindow.cpp',
  'src/GUIWindow.h',
  'src/ListWidget.cpp',
  'src/ListWidget.h',
  'src/MPEGParser.cpp',
  'src/MPEGParser.h',
  'src/ScrollArea.cpp',
  'src/ScrollArea.h',
  processed_files
]

warnings = []
if cxx.get_argument_syntax() == 'gcc'
  warnings += [
    '-Wall',
    '-Wextra',
    '-Wshadow'
  ]
endif

cpp_args = [
  '-DPACKAGE_VERSION="@0@"'.format(meson.project_version()),
  '-DQT_DISABLE_DEPRECATED_UP_TO=0x060000',
  warnings
]

link_args = []
rpath = ''
if host_machine.system() == 'darwin'
  link_args += '-Wl,-dead_strip'
  # Qt frameworks inside d2vwitch.app, put there by macdeployqt
  rpath = '@executable_path/../Frameworks'
endif

executable('d2vwitch',
  sources: sources,
  dependencies: [deps, dep_qt],
  win_subsystem: 'windows',
  cpp_args: cpp_args,
  link_args: link_args,
  build_rpath: rpath,
  install_rpath: rpath,
  install: true
)
