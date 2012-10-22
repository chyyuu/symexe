V ?= @

SRC  := $(shell find . -name "*.rst")
MAN  := ${addprefix build/,${SRC:.rst=.man}}
HTML := ${addprefix build/,${SRC:.rst=.html}}
TEX  := ${addprefix build/,${SRC:.rst=.tex}}

.DEFAULT_GOAL := html
.PHONY: man html tex

man: ${MAN}

html: ${HTML}

tex: ${TEX}

${MAN}:build/%.man:%.rst
	@echo + MAN $<
	@mkdir -p $(dir $@)
	${V}rst2man ${ARGS} $< $@

${HTML}:build/%.html:%.rst
	@echo + HTML $<
	@mkdir -p $(dir $@)
	${V}rst2html ${ARGS} $< $@

${TEX}:build/%.tex:%.rst
	@echo + TEX $<
	@mkdir -p $(dir $@)
	${V}rst2latex ${ARGS} $< $@

clean:
	${V}rm -rf build
