project('Python extension module', 'c',
  default_options : ['buildtype=release', 'werror=true'])
# Because Windows Python ships only with optimized libs,
# we must build this project the same way.

if meson.backend() != 'ninja'
  error('MESON_SKIP_TEST: Ninja backend required')
endif


py_mod = import('python')
py = py_mod.find_installation()
py_dep = py.dependency(required: false)

if not py_dep.found()
  error('MESON_SKIP_TEST: Python libraries not found.')
endif

subdir('ext')

blaster = configure_file(
  input: 'blaster.py.in',
  output: 'blaster.py',
  configuration: {'tachyon_module': 'tachyon'}
)

test('extmod',
  py,
  args : blaster,
  env : ['PYTHONPATH=' + pypathdir])

py.install_sources(blaster, pure: false)
py.install_sources(blaster, subdir: 'pure')

py3_pkg_dep = dependency('python3', method: 'pkg-config', required : false)
if py3_pkg_dep.found()
  py3_dep_majver = py3_pkg_dep.version().split('.')
  py3_dep_majver = py3_dep_majver[0] + '.' + py3_dep_majver[1]
  message(f'got two pythons: pkg-config is @py3_dep_majver@, and module is', py.language_version())
  if py3_dep_majver != py.language_version()
    message('skipped python3 pkg-config test because the default python3 is different from Meson\'s')
  else
    python_lib_dir = py3_pkg_dep.get_pkgconfig_variable('libdir')

    # Check we can apply a version constraint
    dependency('python3', version: '>=@0@'.format(py_dep.version()))
  endif
else
  message('Skipped python3 pkg-config test because it was not found')
endif
