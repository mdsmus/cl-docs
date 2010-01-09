Use Ilisp.

Downloaded it from http://ilisp.cons.org

add to your .emacs:

(setq load-path (cons (expand-file-name "/usr/local/lib/xemacs/ilisp/") 
                      load-path))
(autoload 'cmulisp   "ilisp" "Inferior CMU Common LISP." t)
(setq cmulisp-program "/usr/bin/lisp")
(setq cmulisp-local-source-directory "/usr/src/18f/src/")

;;; This makes reading a lisp file load in ilisp.
(set-default 'auto-mode-alist
	     (append '(("\\.lisp$" . lisp-mode)) auto-mode-alist))
(setq lisp-mode-hook '(lambda ()
			(if (eq window-system 'x)
			    (setq lisp-font-lock-keywords
				  lisp-font-lock-keywords-2))
			(require 'ilisp)))

;;; Sample load hook
(add-hook 'ilisp-load-hook 
	  (function
	   (lambda ()
	     ;; Change default key prefix to C-c
	     (setq ilisp-prefix "\C-c")
	     ;; Make sure that you don't keep popping up the 'inferior
	     ;; lisp' buffer window when this is already visible in
	     ;; another frame. Actually this variable has more impact
	     ;; than that. Watch out.
	     ;(setq pop-up-frames t)

	     (message "Running ilisp-load-hook")
	     ;; Define LispMachine-like key bindings, too.
	     ;; (ilisp-lispm-bindings) Sample initialization hook.
	     
	     (if (eq window-system 'x)
		 (setq lisp-font-lock-keywords lisp-font-lock-keywords-2))

	     ;; Set the inferior LISP directory to the directory of
	     ;; the buffer that spawned it on the first prompt.
	     (add-hook 'ilisp-init-hook
		       (function
			(lambda ()
			  (default-directory-lisp ilisp-last-buffer))))
	     )))

(add-hook 'ilisp-site-hook
	  (function
	   (lambda ()
	     (setq ilisp-init-binary-extension "x86f")
	     (setq ilisp-init-binary-command "(progn \"x86f\")")
	     ;; (setq ilisp-binary-extension "sparcf")
	     )))

(setq cmulisp-source-directory-regexp 
  "\\/usr\\/src\\/cmu\\/")

Start ilisp with M-x cmulisp

Type 

M-Tab to complete your input
M-C-x to eval the current defun
C-c M to macroexpand the current defun
C-c m to macroexpand-1
C-c i to describe something
C-c d to see the documentation about something
C-c c to compile the current defun
C-c l to load the file
C-c k to compile the file
C-c I to inspect something

And if you still want manual control you can just go to the
*cmulisp* buffer.



