scons-doxygen-template
======================

Template module to generate documentation with Doxygen_ and SCons_.
Uses scons_doxygen_ tool to run Doxygen from a SCons script.

Usage example
-------------

Git-based projects
^^^^^^^^^^^^^^^^^^

#. Create new git repository:: 

      mkdir my-project && cd my-project
      touch README.md
      git init

#. Add scons_doxygen_ as submodule (here we use git mirror of scons_doxygen_)::

      git submodule add git@github.com:ptomulik/scons_doxygen.git site_scons/site_tools/doxygen

#. Add scons-doxygen-template_ as submodule::

      git submodule add git@github.com:ptomulik/scons-doxygen-template.git doc

#. Create some source files, for example ``src/test.hpp``:

   .. code-block:: cpp

      // src/test.hpp
      /**
       * @brief Test class
       */
       class TestClass { };

#. Write ``SConstruct`` file:

   .. code-block:: python

      # SConstruct
      env = Environment(tools =  ['doxygen', 'textfile'])
      options = {
        'SRCDIR'                : '#src',
        'PROJECT_NAME'          : 'Test Project 1',
        'PROJECT_NUMBER'        : '0.1.0',
        'PROJECT_BRIEF'         : 'Simple Test Project',
        'RECURSIVE'             : 'YES'
      }
      env.SConscript('doc/SConscript', exports=['env','options'], variant_dir='build/doc', duplicate=0)

      env.Ignore('build','build/doc')
      env.Alias('api-doc', 'build/doc')

#. Try it out::

      scons api-doc

   This shall create documentation under ``build/doc/`` directory.

#. To clean-up the documentation run::

      scons -c api-doc


Details
-------

Template contents and description
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The scons-doxygen-template_ consists of two files:

* ``SConscript`` file, and 
* ``Doxyfile.in`` template.

The ``SConscript`` generates ``Doxyfile`` from ``Doxyfile.in`` and then runs
doxygen to generate documentation. It accepts several ``options`` to customize
the generated ``Doxyfile``. This allows using unmodified template to generate
documentation for several sub-modules in your project by using variant dirs,
for example

.. code-block:: python

  # SConstruct
  env = Environment(tools =  ['doxygen', 'textfile'])
  options = [ {
    'SRCDIR'                : '#src/project1',
    'PROJECT_NAME'          : 'Test Project 1',
    'PROJECT_NUMBER'        : '0.1.0',
    'PROJECT_BRIEF'         : 'Project One',
  }, {
    'SRCDIR'                : '#src/project2',
    'PROJECT_NAME'          : 'Test Project 2',
    'PROJECT_NUMBER'        : '0.2.0',
    'PROJECT_BRIEF'         : 'Project Two',
  } ]

  env.SConscript('doc/SConscript', 
    variant_dir = 'build/doc1',
    exports = {'env' : env, 'options' : options[0]},
    duplicate = 0
  )

  env.SConscript('doc/SConscript', 
    variant_dir = 'build/doc2',
    exports = {'env' : env, 'options' : options[1]},
    duplicate = 0
  )

Of course, you may also put several copies of the template in different places
of your project and customize each of them separately.

Supported options
^^^^^^^^^^^^^^^^^

The following table summarizes options supported by ``SConscript``.  Most of
them are passed directly to the generated ``Doxyfile``.

======================== =========================================================
       Option                               Description
======================== =========================================================
 ``CPP_FILTER``           Script used as a doxygen input filter for C/C++ sources.
 EXAMPLE_PATH_            Passed to Doxyfile.
 EXAMPLE_PATTERNS_        Passed to Doxyfile.
 EXCLUDE_                 Passed to Doxyfile.
 FILE_PATTERNS_           Passed to Doxyfile.
 FILTER_PATTERNS_         Passed to Doxyfile.
 FILTER_SOURCE_FILES_     Passed to Doxyfile,
 FILTER_SOURCE_PATTERNS_  Passed to Doxyfile,
 IMAGE_PATH_              Passed to Doxyfile. 
 OUTPUT_DIRECTORY_        Passed to Doxyfile. 
 PROJECT_BRIEF_           Passed to Doxyfile. 
 PROJECT_LOGO_            Passed to Doxyfile. 
 PROJECT_NAME_            Passed to Doxyfile. 
 PROJECT_NUMBER_          Passed to Doxyfile. 
 ``SRCDIR``               Directory containing source files to process.
 STRIP_FROM_INC_PATH_     Passed to Doxyfile. 
 STRIP_FROM_PATH_         Passed to Doxyfile. 
======================== =========================================================

.. _EXAMPLE_PATH: http://www.stack.nl/dimitri/doxygen/manual/config.html#cfg_example_path
.. _EXAMPLE_PATTERNS: http://www.stack.nl/dimitri/doxygen/manual/config.html#cfg_example_patterns
.. _EXCLUDE: http://www.stack.nl/dimitri/doxygen/manual/config.html#cfg_exclude
.. _FILE_PATTERNS: http://www.stack.nl/dimitri/doxygen/manual/config.html#cfg_file_patterns
.. _FILTER_PATTERNS: http://www.stack.nl/dimitri/doxygen/manual/config.html#cfg_filter_patterns
.. _FILTER_SOURCE_FILES: http://www.stack.nl/dimitri/doxygen/manual/config.html#cfg_filter_source_files
.. _FILTER_SOURCE_PATTERNS: http://www.stack.nl/dimitri/doxygen/manual/config.html#cfg_filter_source_patterns
.. _IMAGE_PATH: http://www.stack.nl/dimitri/doxygen/manual/config.html#cfg_image_path
.. _OUTPUT_DIRECTORY: http://www.stack.nl/dimitri/doxygen/manual/config.html#cfg_output_directory
.. _PROJECT_BRIEF: http://www.stack.nl/dimitri/doxygen/manual/config.html#cfg_project_brief
.. _PROJECT_LOGO: http://www.stack.nl/dimitri/doxygen/manual/config.html#cfg_project_logo
.. _PROJECT_NAME: http://www.stack.nl/dimitri/doxygen/manual/config.html#cfg_project_name
.. _PROJECT_NUMBER: http://www.stack.nl/dimitri/doxygen/manual/config.html#cfg_project_number
.. _STRIP_FROM_INC_PATH: http://www.stack.nl/dimitri/doxygen/manual/config.html#cfg_strip_from_inc_path
.. _STRIP_FROM_PATH: http://www.stack.nl/dimitri/doxygen/manual/config.html#cfg_strip_from_path

Boolean (YES/NO) configuration options:

============================== =========
  Configuration Option          Default
============================== =========
  ALLEXTERNALS_                 ``NO`` 
  ALPHABETICAL_INDEX_           ``YES``
  ALWAYS_DETAILED_SEC_          ``NO`` 
  AUTOLINK_SUPPORT_             ``YES``
  BINARY_TOC_                   ``NO`` 
  BRIEF_MEMBER_DESC_            ``YES``
  BUILTIN_STL_SUPPORT_          ``NO`` 
  CALLER_GRAPH_                 ``NO`` 
  CALL_GRAPH_                   ``NO`` 
  CLANG_ASSISTED_PARSING_       ``NO`` 
  CLASS_DIAGRAMS_               ``YES``
  CLASS_GRAPH_                  ``YES``
  COLLABORATION_GRAPH_          ``YES``
  COMPACT_LATEX_                ``NO`` 
  COMPACT_RTF_                  ``NO`` 
  CPP_CLI_SUPPORT_              ``NO`` 
  CREATE_SUBDIRS_               ``NO`` 
  DIRECTORY_GRAPH_              ``YES``
  DISABLE_INDEX_                ``NO`` 
  DISTRIBUTE_GROUP_DOC_         ``NO`` 
  DOT_CLEANUP_                  ``YES``
  DOT_MULTI_TARGETS_            ``NO`` 
  DOT_TRANSPARENT_              ``NO`` 
  ENABLE_PREPROCESSING_         ``YES``
  EXAMPLE_RECURSIVE_            ``NO`` 
  EXCLUDE_SYMLINKS_             ``NO`` 
  EXPAND_ONLY_PREDEF_           ``NO`` 
  EXTERNAL_GROUPS_              ``YES``
  EXTERNAL_PAGES_               ``YES``
  EXTERNAL_SEARCH_              ``NO`` 
  EXTRACT_ALL_                  ``NO`` 
  EXTRACT_ANON_NSPACES_         ``NO`` 
  EXTRACT_LOCAL_CLASSES_        ``YES``
  EXTRACT_LOCAL_METHODS_        ``NO`` 
  EXTRACT_PACKAGE_              ``NO`` 
  EXTRACT_PRIVATE_              ``NO`` 
  EXTRACT_STATIC_               ``NO`` 
  EXT_LINKS_IN_WINDOW_          ``NO`` 
  FILTER_SOURCE_FILES_          ``NO`` 
  FORCE_LOCAL_INCLUDES_         ``NO`` 
  FORMULA_TRANSPARENT_          ``YES``
  FULL_PATH_NAMES_              ``YES``
  GENERATE_AUTOGEN_DEF_         ``NO`` 
  GENERATE_BUGLIST_             ``YES``
  GENERATE_CHI_                 ``NO`` 
  GENERATE_DEPRECATEDLIST_      ``YES``
  GENERATE_DOCBOOK_             ``NO`` 
  GENERATE_DOCSET_              ``NO`` 
  GENERATE_ECLIPSEHELP_         ``NO`` 
  GENERATE_HTML_                ``YES``
  GENERATE_HTMLHELP_            ``NO`` 
  GENERATE_LATEX_               ``YES``
  GENERATE_LEGEND_              ``YES``
  GENERATE_MAN_                 ``NO`` 
  GENERATE_PERLMOD_             ``NO`` 
  GENERATE_QHP_                 ``NO`` 
  GENERATE_RTF_                 ``NO`` 
  GENERATE_TESTLIST_            ``YES``
  GENERATE_TODOLIST_            ``YES``
  GENERATE_TREEVIEW_            ``NO`` 
  GENERATE_XML_                 ``NO`` 
  GRAPHICAL_HIERARCHY_          ``YES``
  GROUP_GRAPHS_                 ``YES``
  HAVE_DOT_                     ``NO`` 
  HIDE_FRIEND_COMPOUNDS_        ``NO`` 
  HIDE_IN_BODY_DOCS_            ``NO`` 
  HIDE_SCOPE_NAMES_             ``NO`` 
  HIDE_UNDOC_CLASSES_           ``NO`` 
  HIDE_UNDOC_MEMBERS_           ``NO`` 
  HIDE_UNDOC_RELATIONS_         ``YES``
  HTML_DYNAMIC_SECTIONS_        ``NO`` 
  HTML_TIMESTAMP_               ``YES``
  IDL_PROPERTY_SUPPORT_         ``YES``
  INCLUDED_BY_GRAPH_            ``YES``
  INCLUDE_GRAPH_                ``YES``
  INHERIT_DOCS_                 ``YES``
  INLINE_GROUPED_CLASSES_       ``NO`` 
  INLINE_INFO_                  ``YES``
  INLINE_INHERITED_MEMB_        ``NO`` 
  INLINE_SIMPLE_STRUCTS_        ``NO`` 
  INLINE_SOURCES_               ``NO`` 
  INTERACTIVE_SVG_              ``NO`` 
  INTERNAL_DOCS_                ``NO`` 
  JAVADOC_AUTOBRIEF_            ``NO`` 
  LATEX_BATCHMODE_              ``NO`` 
  LATEX_HIDE_INDICES_           ``NO`` 
  LATEX_SOURCE_CODE_            ``NO`` 
  MACRO_EXPANSION_              ``NO`` 
  MAN_LINKS_                    ``NO`` 
  MARKDOWN_SUPPORT_             ``YES``
  MULTILINE_CPP_IS_BRIEF_       ``NO`` 
  OPTIMIZE_FOR_FORTRAN_         ``NO`` 
  OPTIMIZE_OUTPUT_FOR_C_        ``NO`` 
  OPTIMIZE_OUTPUT_JAVA_         ``NO`` 
  OPTIMIZE_OUTPUT_VHDL_         ``NO`` 
  PDF_HYPERLINKS_               ``YES``
  PERLMOD_LATEX_                ``NO`` 
  PERLMOD_PRETTY_               ``YES``
  QT_AUTOBRIEF_                 ``NO`` 
  QUIET_                        ``NO`` 
  RECURSIVE_                    ``NO`` 
  REFERENCED_BY_RELATION_       ``NO`` 
  REFERENCES_LINK_SOURCE_       ``YES``
  REFERENCES_RELATION_          ``NO`` 
  REPEAT_BRIEF_                 ``YES``
  RTF_HYPERLINKS_               ``NO`` 
  SEARCHENGINE_                 ``YES``
  SEARCH_INCLUDES_              ``YES``
  SEPARATE_MEMBER_PAGES_        ``NO`` 
  SERVER_BASED_SEARCH_          ``NO`` 
  SHORT_NAMES_                  ``NO`` 
  SHOW_FILES_                   ``YES``
  SHOW_INCLUDE_FILES_           ``YES``
  SHOW_NAMESPACES_              ``YES``
  SHOW_USED_FILES_              ``YES``
  SIP_SUPPORT_                  ``NO`` 
  SKIP_FUNCTION_MACROS_         ``YES``
  SORT_BRIEF_DOCS_              ``NO`` 
  SORT_BY_SCOPE_NAME_           ``NO`` 
  SORT_GROUP_NAMES_             ``NO`` 
  SORT_MEMBERS_CTORS_1ST_       ``NO`` 
  SORT_MEMBER_DOCS_             ``YES``
  SOURCE_BROWSER_               ``NO`` 
  SOURCE_TOOLTIPS_              ``YES``
  STRICT_PROTO_MATCHING_        ``NO`` 
  STRIP_CODE_COMMENTS_          ``YES``
  SUBGROUPING_                  ``YES``
  TEMPLATE_RELATIONS_           ``NO`` 
  TOC_EXPAND_                   ``NO`` 
  TYPEDEF_HIDES_STRUCT_         ``NO`` 
  UML_LOOK_                     ``NO`` 
  USE_MATHJAX_                  ``NO`` 
  USE_PDFLATEX_                 ``YES``
  VERBATIM_HEADERS_             ``YES``
  WARNINGS_                     ``YES``
  WARN_IF_DOC_ERROR_            ``YES``
  WARN_IF_UNDOCUMENTED_         ``YES``
  WARN_NO_PARAMDOC_             ``NO`` 
  XML_PROGRAMLISTING_           ``YES``
============================== =========

.. <!-- boolean (YES/NO) configuration options -->
.. _ALLEXTERNALS: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_allexternals
.. _ALPHABETICAL_INDEX: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_alphabetical_index
.. _ALWAYS_DETAILED_SEC: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_always_detailed_sec
.. _AUTOLINK_SUPPORT: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_autolink_support
.. _BINARY_TOC: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_binary_toc
.. _BRIEF_MEMBER_DESC: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_brief_member_desc
.. _BUILTIN_STL_SUPPORT: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_builtin_stl_support
.. _CALLER_GRAPH: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_caller_graph
.. _CALL_GRAPH: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_call_graph
.. _CLANG_ASSISTED_PARSING: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_clang_assisted_parsing
.. _CLASS_DIAGRAMS: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_class_diagrams
.. _CLASS_GRAPH: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_class_graph
.. _COLLABORATION_GRAPH: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_collaboration_graph
.. _COMPACT_LATEX: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_compact_latex
.. _COMPACT_RTF: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_compact_rtf
.. _CPP_CLI_SUPPORT: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_cpp_cli_support
.. _CREATE_SUBDIRS: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_create_subdirs
.. _DIRECTORY_GRAPH: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_directory_graph
.. _DISABLE_INDEX: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_disable_index
.. _DISTRIBUTE_GROUP_DOC: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_distribute_group_doc
.. _DOT_CLEANUP: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_dot_cleanup
.. _DOT_MULTI_TARGETS: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_dot_multi_targets
.. _DOT_TRANSPARENT: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_dot_transparent
.. _ENABLE_PREPROCESSING: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_enable_preprocessing
.. _EXAMPLE_RECURSIVE: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_example_recursive
.. _EXCLUDE_SYMLINKS: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_exclude_symlinks
.. _EXPAND_ONLY_PREDEF: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_expand_only_predef
.. _EXTERNAL_GROUPS: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_external_groups
.. _EXTERNAL_PAGES: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_external_pages
.. _EXTERNAL_SEARCH: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_external_search
.. _EXTRACT_ALL: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_extract_all
.. _EXTRACT_ANON_NSPACES: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_extract_anon_nspaces
.. _EXTRACT_LOCAL_CLASSES: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_extract_local_classes
.. _EXTRACT_LOCAL_METHODS: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_extract_local_methods
.. _EXTRACT_PACKAGE: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_extract_package
.. _EXTRACT_PRIVATE: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_extract_private
.. _EXTRACT_STATIC: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_extract_static
.. _EXT_LINKS_IN_WINDOW: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_ext_links_in_window
.. _FILTER_SOURCE_FILES: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_filter_source_files
.. _FORCE_LOCAL_INCLUDES: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_force_local_includes
.. _FORMULA_TRANSPARENT: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_formula_transparent
.. _FULL_PATH_NAMES: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_full_path_names
.. _GENERATE_AUTOGEN_DEF: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_generate_autogen_def
.. _GENERATE_BUGLIST: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_generate_buglist
.. _GENERATE_CHI: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_generate_chi
.. _GENERATE_DEPRECATEDLIST: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_generate_deprecatedlist
.. _GENERATE_DOCBOOK: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_generate_docbook
.. _GENERATE_DOCSET: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_generate_docset
.. _GENERATE_ECLIPSEHELP: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_generate_eclipsehelp
.. _GENERATE_HTML: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_generate_html
.. _GENERATE_HTMLHELP: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_generate_htmlhelp
.. _GENERATE_LATEX: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_generate_latex
.. _GENERATE_LEGEND: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_generate_legend
.. _GENERATE_MAN: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_generate_man
.. _GENERATE_PERLMOD: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_generate_perlmod
.. _GENERATE_QHP: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_generate_qhp
.. _GENERATE_RTF: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_generate_rtf
.. _GENERATE_TESTLIST: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_generate_testlist
.. _GENERATE_TODOLIST: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_generate_todolist
.. _GENERATE_TREEVIEW: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_generate_treeview
.. _GENERATE_XML: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_generate_xml
.. _GRAPHICAL_HIERARCHY: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_graphical_hierarchy
.. _GROUP_GRAPHS: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_group_graphs
.. _HAVE_DOT: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_have_dot
.. _HIDE_FRIEND_COMPOUNDS: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_hide_friend_compounds
.. _HIDE_IN_BODY_DOCS: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_hide_in_body_docs
.. _HIDE_SCOPE_NAMES: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_hide_scope_names
.. _HIDE_UNDOC_CLASSES: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_hide_undoc_classes
.. _HIDE_UNDOC_MEMBERS: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_hide_undoc_members
.. _HIDE_UNDOC_RELATIONS: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_hide_undoc_relations
.. _HTML_DYNAMIC_SECTIONS: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_html_dynamic_sections
.. _HTML_TIMESTAMP: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_html_timestamp
.. _IDL_PROPERTY_SUPPORT: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_idl_property_support
.. _INCLUDED_BY_GRAPH: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_included_by_graph
.. _INCLUDE_GRAPH: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_include_graph
.. _INHERIT_DOCS: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_inherit_docs
.. _INLINE_GROUPED_CLASSES: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_inline_grouped_classes
.. _INLINE_INFO: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_inline_info
.. _INLINE_INHERITED_MEMB: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_inline_inherited_memb
.. _INLINE_SIMPLE_STRUCTS: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_inline_simple_structs
.. _INLINE_SOURCES: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_inline_sources
.. _INTERACTIVE_SVG: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_interactive_svg
.. _INTERNAL_DOCS: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_internal_docs
.. _JAVADOC_AUTOBRIEF: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_javadoc_autobrief
.. _LATEX_BATCHMODE: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_latex_batchmode
.. _LATEX_HIDE_INDICES: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_latex_hide_indices
.. _LATEX_SOURCE_CODE: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_latex_source_code
.. _MACRO_EXPANSION: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_macro_expansion
.. _MAN_LINKS: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_man_links
.. _MARKDOWN_SUPPORT: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_markdown_support
.. _MULTILINE_CPP_IS_BRIEF: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_multiline_cpp_is_brief
.. _OPTIMIZE_FOR_FORTRAN: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_optimize_for_fortran
.. _OPTIMIZE_OUTPUT_FOR_C: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_optimize_output_for_c
.. _OPTIMIZE_OUTPUT_JAVA: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_optimize_output_java
.. _OPTIMIZE_OUTPUT_VHDL: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_optimize_output_vhdl
.. _PDF_HYPERLINKS: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_pdf_hyperlinks
.. _PERLMOD_LATEX: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_perlmod_latex
.. _PERLMOD_PRETTY: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_perlmod_pretty
.. _QT_AUTOBRIEF: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_qt_autobrief
.. _QUIET: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_quiet
.. _RECURSIVE: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_recursive
.. _REFERENCED_BY_RELATION: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_referenced_by_relation
.. _REFERENCES_LINK_SOURCE: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_references_link_source
.. _REFERENCES_RELATION: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_references_relation
.. _REPEAT_BRIEF: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_repeat_brief
.. _RTF_HYPERLINKS: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_rtf_hyperlinks
.. _SEARCHENGINE: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_searchengine
.. _SEARCH_INCLUDES: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_search_includes
.. _SEPARATE_MEMBER_PAGES: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_separate_member_pages
.. _SERVER_BASED_SEARCH: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_server_based_search
.. _SHORT_NAMES: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_short_names
.. _SHOW_FILES: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_show_files
.. _SHOW_INCLUDE_FILES: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_show_include_files
.. _SHOW_NAMESPACES: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_show_namespaces
.. _SHOW_USED_FILES: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_show_used_files
.. _SIP_SUPPORT: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_sip_support
.. _SKIP_FUNCTION_MACROS: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_skip_function_macros
.. _SORT_BRIEF_DOCS: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_sort_brief_docs
.. _SORT_BY_SCOPE_NAME: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_sort_by_scope_name
.. _SORT_GROUP_NAMES: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_sort_group_names
.. _SORT_MEMBERS_CTORS_1ST: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_sort_members_ctors_1st
.. _SORT_MEMBER_DOCS: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_sort_member_docs
.. _SOURCE_BROWSER: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_source_browser
.. _SOURCE_TOOLTIPS: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_source_tooltips
.. _STRICT_PROTO_MATCHING: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_strict_proto_matching
.. _STRIP_CODE_COMMENTS: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_strip_code_comments
.. _SUBGROUPING: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_subgrouping
.. _TEMPLATE_RELATIONS: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_template_relations
.. _TOC_EXPAND: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_toc_expand
.. _TYPEDEF_HIDES_STRUCT: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_typedef_hides_struct
.. _UML_LOOK: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_uml_look
.. _USE_MATHJAX: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_use_mathjax
.. _USE_PDFLATEX: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_use_pdflatex
.. _VERBATIM_HEADERS: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_verbatim_headers
.. _WARNINGS: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_warnings
.. _WARN_IF_DOC_ERROR: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_warn_if_doc_error
.. _WARN_IF_UNDOCUMENTED: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_warn_if_undocumented
.. _WARN_NO_PARAMDOC: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_warn_no_paramdoc
.. _XML_PROGRAMLISTING: http://www.stack.nl/~dimitri/doxygen/manual/config.html#cfg_xml_programlisting


.. <!-- Other links -->
.. _SCons: http://scons.org
.. _Doxygen: http://doxygen.org
.. _scons_doxygen: https://bitbucket.org/russel/scons_doxygen
.. _scons-doxygen-template: https://github.com/ptomulik/scons-doxygen-template

LICENSE
-------

Copyright (c) 2013 by Pawel Tomulik <ptomulik@meil.pw.edu.pl>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE

.. <!--- vim: set expandtab tabstop=2 shiftwidth=2 syntax=rst: -->
