project('mimic-cmu_us_slt', 'c', default_options : ['c_std=c99'], version: '0')
voice_name = 'cmu_us_slt'

build_plugin = get_option('plugin')

sources = [
  'src/cmu_us_slt.c',
  'src/cmu_us_slt_cg.c',
  'src/cmu_us_slt_cg_durmodel.c',
  'src/cmu_us_slt_cg_durmodel.h',
  'src/cmu_us_slt_cg_f0_trees.c',
  'src/cmu_us_slt_cg_f0_trees.h',
  'src/cmu_us_slt_cg_phonestate.c',
  'src/cmu_us_slt_cg_single_mcep_trees.c',
  'src/cmu_us_slt_cg_single_mcep_trees.h',
  'src/cmu_us_slt_cg_single_params.c',
  'src/cmu_us_slt_spamf0_accent.c',
  'src/cmu_us_slt_spamf0_accent.h',
  'src/cmu_us_slt_spamf0_accent_params.c',
  'src/cmu_us_slt_spamf0_phrase.c',
  'src/cmu_us_slt_spamf0_phrase.h',
  'src/voxdefs.h'
]

includes = [include_directories('include')]

# Core Dependency:
mimiccore = dependency('ttsmimic_core')
mimic_plugin_dir = mimiccore.get_pkgconfig_variable('mimic_plugin_dir')
mimicenglish = dependency('ttsmimic_english')

# If mimic-core does not have plugin support, disable the plugin
if mimic_plugin_dir == ''
  build_plugin = false
endif

message('Build mimic-@0@ plugin: @1@'.format(voice_name, build_plugin))


en_us_header_subdir = join_paths('ttsmimic', 'lang', 'en_US')

conf_data = configuration_data()
conf_data.set('ENABLE_@0@_BUILTIN'.format(voice_name.to_upper()), not build_plugin)

config_h = configure_file(output : 'mimic_@0@_config.h'.format(voice_name),
                          configuration : conf_data, install: true,
                          install_dir : join_paths(get_option('includedir'), en_us_header_subdir))

sources += [config_h]

voice_lib = library('ttsmimic_@0@'.format(voice_name), sources,
                    include_directories : includes,
                    dependencies: [mimicenglish, mimiccore],
                    install: true)

voice_dep = declare_dependency(link_with : voice_lib, 
                               include_directories : includes)


if build_plugin
  voice_plugin_lib = library(
    'ttsmimic_@0@_plugin'.format(voice_name),
    ['src/@0@_plugin.c'.format(voice_name), config_h],
    include_directories : includes,
    dependencies: [mimiccore, mimicenglish, voice_dep],
    install_dir: mimic_plugin_dir,
    install: true)

  voice_plugin_dep = declare_dependency(
    link_with : voice_plugin_lib, 
    include_directories : includes)
endif



############ Headers #################

ttsmimic_headers = ['include/@0@.h'.format(voice_name)]

install_headers(ttsmimic_headers, subdir : en_us_header_subdir)


########### pkg-config #############

pkg = import('pkgconfig')

pkg.generate(libraries : voice_lib,
             name : 'ttsmimic_@0@'.format(voice_name),
             subdirs: en_us_header_subdir,
             requires: ['ttsmimic_english', 'ttsmimic_core'],
             description: '@0@ support for text to speech synthesis in mimic.'.format(voice_name),
             version: meson.project_version())

