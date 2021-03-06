# vim: noet ts=8
# Makefile to run all tests for Vim
#

ifeq ($(OS),Windows_NT)
  NVIM_PRG ?= ../../../build/bin/nvim.exe
else
  NVIM_PRG ?= ../../../build/bin/nvim
endif
ROOT := ../../..

export SHELL := sh
export NVIM_PRG := $(NVIM_PRG)
export TMPDIR := $(abspath ../../../Xtest-tmpdir)

SCRIPTS_DEFAULT = \
	test14.out             \
	test24.out             \
	test37.out             \
	test42.out             \
	test48.out             \
	test52.out             \
	test64.out             \

ifneq ($(OS),Windows_NT)
  SCRIPTS_DEFAULTS := $(SCRIPTS_DEFAULT) \
	test17.out             \
	test49.out             \

endif

SCRIPTS ?= $(SCRIPTS_DEFAULT)

# Tests using runtest.vim.
# Keep test_alot*.res as the last one, sort the others.
NEW_TESTS ?= \
	test_arabic.res \
	test_autocmd.res \
	test_blockedit.res \
	test_bufwintabinfo.res \
	test_changedtick.res \
	test_charsearch.res \
	test_cindent.res \
	test_clientserver.res \
	test_close_count.res \
	test_cmdline.res \
	test_command_count.res \
	test_cscope.res \
	test_curswant.res \
	test_digraph.res \
	test_edit.res \
	test_erasebackword.res \
	test_exists.res \
	test_diffmode.res \
	test_farsi.res \
	test_file_size.res \
	test_filter_map.res \
	test_find_complete.res \
	test_fixeol.res \
	test_findfile.res \
	test_fnameescape.res \
	test_fold.res \
	test_ga.res \
	test_getvar.res \
	test_glob2regpat.res \
	test_gf.res \
	test_gn.res \
	test_hardcopy.res \
	test_help_tagjump.res \
	test_hide.res \
	test_highlight.res \
	test_history.res \
	test_hlsearch.res \
	test_increment.res \
	test_increment_dbcs.res \
	test_ins_complete.res \
	test_lambda.res \
	test_langmap.res \
	test_let.res \
	test_lineending.res \
	test_listdict.res \
	test_listchars.res \
	test_makeencoding.res \
	test_marks.res \
	test_match.res \
	test_matchadd_conceal.res \
	test_mksession.res \
	test_nested_function.res \
	test_normal.res \
	test_number.res \
	test_options.res \
	test_preview.res \
	test_profile.res \
	test_put.res \
	test_python2.res \
	test_python3.res \
	test_quickfix.res \
	test_quotestar.res \
	test_recover.res \
	test_registers.res \
	test_retab.res \
	test_scrollbind.res \
	test_search.res \
	test_signs.res \
	test_smartindent.res \
	test_spell.res \
	test_stat.res \
	test_startup.res \
	test_substitute.res \
	test_swap.res \
	test_syntax.res \
	test_system.res \
	test_tab.res \
	test_tabpage.res \
	test_textobjects.res \
	test_timers.res \
	test_undo.res \
	test_usercommands.res \
	test_user_func.res \
	test_vimscript.res \
	test_visual.res \
	test_winbuf_close.res \
	test_window_id.res \
	test_windows_home.res \
	test_wordcount.res \
	test_writefile.res \
	test_alot_latin.res \
	test_alot_utf8.res \
	test_alot.res

SCRIPTS_GUI := test16.out


ifdef VALGRIND_GDB
	VGDB := --vgdb=yes     \
	        --vgdb-error=0
endif

ifdef USE_VALGRIND
	VALGRIND_TOOL := --tool=memcheck     \
	                 --leak-check=yes    \
	                 --track-origins=yes
#        VALGRIND_TOOL := exp-sgcheck
	TOOL := valgrind -q                                     \
	                 -q                                     \
	                 $(VALGRIND_TOOL)                       \
	                 --suppressions=../../.valgrind.supp \
	                 --error-exitcode=123                   \
	                 --log-file=valgrind-\%p.$*             \
	                 $(VGDB)                                \
	                 --trace-children=yes
else
	ifdef USE_GDB
		TOOL = gdb --args
	endif
endif

ifdef TESTNUM
	SCRIPTS := test$(TESTNUM).out
endif

nongui: nolog $(SCRIPTS) newtests report

gui:    nolog $(SCRIPTS) $(SCRIPTS_GUI) newtests report

.gdbinit:
	@echo "[OLDTEST-PREP] Setting up .gdbinit"
	@echo 'set $$_exitcode = -1\nrun\nif $$_exitcode != -1\n  quit\nend' > .gdbinit

report:
	@echo
	@echo 'Test results:'
	@/bin/sh -c "if test -f test.log; then \
	                 cat test.log;         \
	                 echo TEST FAILURE;    \
	                 exit 1;               \
	             else                      \
	                 echo ALL DONE;        \
	             fi"

test1.out: $(NVIM_PRG)

$(SCRIPTS) $(SCRIPTS_GUI): $(NVIM_PRG) test1.out

RM_ON_RUN   := test.out X* viminfo
RM_ON_START := test.ok
RUN_VIM     := $(TOOL) $(NVIM_PRG) -u unix.vim -U NONE -i viminfo --headless --noplugin -s dotest.in

clean:
	-rm -rf *.out          \
	        *.failed       \
	        *.res          \
	        *.rej          \
	        *.orig         \
	        *.tlog         \
	        test.log       \
	        messages       \
	        $(RM_ON_RUN)   \
	        $(RM_ON_START) \
	        valgrind.*     \
	        .*.swp         \
	        .*.swo         \
	        .gdbinit       \
	        $(TMPDIR)      \
	        del

test1.out: .gdbinit test1.in
	@echo "[OLDTEST-PREP] Running test1"
	@rm -rf $*.failed $(RM_ON_RUN) $(RM_ON_START) wrongtermsize
	@mkdir -p $(TMPDIR)
	@/bin/sh runnvim.sh $(ROOT) $(NVIM_PRG) $* $(RUN_VIM) $*.in
	@rm -f wrongtermsize
	@rm -rf X* viminfo

%.out: %.in .gdbinit
	@echo "[OLDESTTEST] Running" $*
	@rm -rf $*.failed test.ok $(RM_ON_RUN)
	@mkdir -p $(TMPDIR)
	@cp $*.ok test.ok
	@/bin/sh runnvim.sh --oldesttest $(ROOT) $(NVIM_PRG) $* $(RUN_VIM) $*.in
	@rm -rf X* test.ok viminfo

test49.out: test49.vim

nolog:
	@echo "[OLDTEST-PREP] Removing test.log and messages"
	@rm -f test.log messages


# New style of tests uses Vim script with assert calls.  These are easier
# to write and a lot easier to read and debug.
# Limitation: Only works with the +eval feature.
RUN_VIMTEST = $(TOOL) $(NVIM_PRG) -u unix.vim -U NONE --headless --noplugin

newtests: newtestssilent
	@/bin/sh -c "if test -f messages && grep -q 'FAILED' messages; then \
	                 cat messages && cat test.log;                      \
	             fi"

newtestssilent: $(NEW_TESTS)

%.res: %.vim .gdbinit
	@echo "[OLDTEST] Running" $*
	@mkdir -p $(TMPDIR)
	@/bin/sh runnvim.sh $(ROOT) $(NVIM_PRG) $* $(RUN_VIMTEST) -u NONE -S runtest.vim $*.vim
