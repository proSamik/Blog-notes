test code

```typescript

import React, { RefObject, useRef  } from 'react';
import ReactDOM from 'react-dom/client';
import { Prism as SyntaxHighlighter } from 'react-syntax-highlighter';
import { oneDark, oneLight } from 'react-syntax-highlighter/dist/cjs/styles/prism';
import { toPng } from 'html-to-image';

// Header component for code blocks
const CodeBlockHeader = ({ language }: { language: string }) => {
    return (
        <div className="h-8 bg-[#f8f8f8] dark:bg-[#282c34] rounded-t-lg">
            <div className="flex items-center justify-between h-full">
                <div className="flex items-center gap-2 px-4">
                    <div className="w-3 h-3 rounded-full bg-red-500 opacity-75 hover:opacity-100 transition-opacity duration-200" />
                    <div className="w-3 h-3 rounded-full bg-yellow-400 opacity-75 hover:opacity-100 transition-opacity duration-200" />
                    <div className="w-3 h-3 rounded-full bg-green-500 opacity-75 hover:opacity-100 transition-opacity duration-200" />
                </div>
                {language && (
                    <div className="px-4 mb-1">
                        <span className="px-2.5 my-1 text-xs font-medium rounded-md text-gray-600 dark:text-gray-400 border border-gray-300 dark:border-gray-600">
                            {language}
                        </span>
                    </div>
                )}
            </div>
        </div>
    );
};


// Main component that includes both syntax highlighting and styling
const CodeBlock = ({ language, code }: { language: string; code: string }) => {
    const codeBlockRef = useRef<HTMLDivElement>(null);

    return (
        <div ref={codeBlockRef} className="p-2 dark:bg-gray-300 rounded relative group">
            <div className="bg-[#f8f8f8] dark:bg-[#282c34] rounded-lg overflow-hidden">
                <CodeBlockHeader language={language}/>
                <ThemedSyntaxHighlighter language={language} code={code}/>
            </div>
        </div>
    );
};

```
