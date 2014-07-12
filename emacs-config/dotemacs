(load-file "~/.emacs.d/plugins/google-c-style.el")
(load-file "~/.emacs.d/plugins/lua-mode.el")
(require 'google-c-style)
(add-hook 'c-mode-common-hook 'google-set-c-style)
(add-hook 'c-mode-common-hook 'google-make-newline-indent)

(set-background-color "dim gray") ;; 使用黑色背景
(set-foreground-color "white") ;; 使用白色前景

(set-face-foreground 'region "magenta")  ;; 区域前景颜色设为红色
(set-face-background 'region "cyan")
(put 'upcase-region 'disabled nil)

(set-default-font "Ubuntu Mono-13")

;;不产生备份文件和临时文件
(setq make-backup-files nil)

;;设置tab为4个空格的宽度
(setq c-basic-offset 4)
(setq default-tab-width 8)
(setq-default indent-tabs-mode nil)

;(setq x-select-enable-clipboard t)

; deal with white spaces
(require 'whitespace)
(global-whitespace-mode)
(setq whitespace-style
      '(face trailing tabs lines lines-tail empty
             space-after-tab space-before-tab))
(add-hook 'before-save-hook 'delete-trailing-whitespace)

;; Show line number
(require 'linum)
(global-linum-mode t)
(setq column-number-mode t)
(setq line-number-node t)
(setq linum-format "%5d ")
(add-hook 'linum-before-numbering-hook
          (lambda ()
            (let ((w (+
                      (length
                       (number-to-string
                        (count-lines (point-min) (point-max))
                        ))
                      2)
                     ))
              (setq linum-format
                    `(lambda (line)
                       (propertize (concat
                                    (truncate-string-to-width
                                     "" (- ,w (length (number-to-string line)))
                                     nil ?x2007)
                                    (number-to-string line))
                                   'face 'linum))))))
(put 'set-goal-column 'disabled nil)
