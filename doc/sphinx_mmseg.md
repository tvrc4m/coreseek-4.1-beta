
#if USE_MMSEG

/// create UTF-8 tokenizer with Chinese Segment support
ISphTokenizer *			sphCreateUTF8ChineseTokenizer ( const char* dict_path );

#endif

#if USE_MMSEG
#include "tokenizer_zhcn.h"


class CSphTokenizer_UTF8MMSeg : public CSphTokenizer_UTF8
{

public:
								CSphTokenizer_UTF8MMSeg ();
								~CSphTokenizer_UTF8MMSeg () {
									if(d_) delete d_;
								}
	virtual void				SetBuffer ( BYTE * sBuffer, int iLength );
	virtual BYTE *				GetToken ();

	virtual ISphTokenizer *		Clone ( bool bEscaped ) const;

	void setDictPath(const char* path) {	m_dictpath = path; }
public:
	virtual const BYTE*				GetThesaurus(BYTE * sBuffer, int iLength );

	virtual const char *	GetBufferPtr () const		{ 
		return (const char *) m_pCur; 
	}

	virtual const char *			GetTokenStart () const		{ 
		return m_segToken; 
	}
	
	virtual int						GetLastTokenLen () const { return m_iLastTokenLenMMSeg; }

protected:
	char* m_segToken;

protected:
	virtual bool				IsSegment(const BYTE * pCur);
	CSphString m_dictpath;
	BYTE				m_sAccumSeg [ 3*SPH_MAX_WORD_LEN+3 ];	///< folded token accumulator
	BYTE *				m_pAccumSeg;							///< current accumulator position
protected:
	CSphTokenizer_zh_CN_UTF8_Private* d_;
	size_t m_segoffset;
	int					m_iLastTokenLenMMSeg;			///< last token length, in codepoints
};

#endif

#if USE_MMSEG 

ISphTokenizer *	sphCreateUTF8ChineseTokenizer ( const char* dict_path )
{
	CSphTokenizer_UTF8MMSeg* tokenizer = new CSphTokenizer_UTF8MMSeg ();
	tokenizer->setDictPath(dict_path);
	return tokenizer;
}

#endif 

#if USE_MMSEG
//////////////////////////////////////////////////////////////////////////
CSphTokenizer_UTF8MMSeg::CSphTokenizer_UTF8MMSeg ()
		:CSphTokenizer_UTF8(),
		m_segoffset(0)
{
	m_dictpath = NULL;
	d_ = new CSphTokenizer_zh_CN_UTF8_Private();
	//FIXCJK
	CSphVector<CSphRemapRange> dRemaps;
	/*
		
	*/
	dRemaps.Add ( CSphRemapRange ( 0x4e00, 0x9fff, 0x4e00 ) );
	dRemaps.Add ( CSphRemapRange ( 0xFF00, 0xFFFF, 0xFF00 ) );
	dRemaps.Add ( CSphRemapRange ( 0x3000, 0x303F, 0x3000 ) );
		
	m_tLC.AddRemaps ( dRemaps,
		FLAG_CODEPOINT_NGRAM | FLAG_CODEPOINT_SPECIAL ); // !COMMIT support other n-gram lengths than 1
	//ENDCJK
	this->m_pAccumSeg = m_sAccumSeg;
	m_iLastTokenBufferLen = 0;
	m_iLastTokenLenMMSeg = 0;	
}

void CSphTokenizer_UTF8MMSeg::SetBuffer ( BYTE * sBuffer, int iLength )
{
	CSphTokenizer_UTF8::SetBuffer(sBuffer, iLength);
	css::Segmenter* seg = d_->GetSegmenter(m_dictpath.cstr());
	if(seg)
		seg->setBuffer((u1*)m_pBuffer, iLength);
	else
		sphDie ( " Tokenizer initialization failure. " );
	m_segoffset = 0;
	m_segToken = (char*)m_pCur;
}

bool	CSphTokenizer_UTF8MMSeg::IsSegment(const BYTE * pCur)
{
	size_t offset = pCur - m_pBuffer;
	//if(offset == 0)	return false;

	css::Segmenter* seg = d_->GetSegmenter(m_dictpath.cstr()); //TODO fill blank here
	if(seg){
		u2 len = 0, symlen = 0;
		const char* tok = NULL;
		while(m_segoffset < offset) {
			tok = (const char*)seg->peekToken(len, symlen);
			seg->popToken(len);
			m_segoffset += len;
			if(tok == NULL || len==0){
				//break?
				break; 
			}
		}
		return (m_segoffset == offset);
	} //end if seg
	return true;
}

BYTE *	CSphTokenizer_UTF8MMSeg::GetToken ()
{
	m_iLastTokenLenMMSeg = 0;
	//BYTE* tok = CSphTokenizer_UTF8::GetToken();
	while(!IsSegment(m_pCur) || m_pAccumSeg == m_sAccumSeg)
	{
		BYTE* tok = CSphTokenizer_UTF8::GetToken();
		if(!tok){
			m_iLastTokenLenMMSeg = 0;
			return NULL;
		}
		
		if(m_pAccumSeg == m_sAccumSeg)
			m_segToken = (char*)m_pTokenStart;
		
		if ( (m_pAccumSeg - m_sAccumSeg)<SPH_MAX_WORD_LEN )  {
			::memcpy(m_pAccumSeg, tok, m_iLastTokenBufferLen);
			m_pAccumSeg += m_iLastTokenBufferLen;
			m_iLastTokenLenMMSeg += m_iLastTokenLen;
		}
	}
	{
		*m_pAccumSeg = 0;
		m_iLastTokenBufferLen = m_pAccumSeg - m_sAccumSeg;
		m_pAccumSeg = m_sAccumSeg;
		
		//m_segToken = (char*)(m_pTokenEnd-m_iLastTokenBufferLen);
		return m_sAccumSeg;
	}
	//return NULL;
}

ISphTokenizer * CSphTokenizer_UTF8MMSeg::Clone ( bool bEscaped ) const
{
	CSphTokenizer_UTF8MMSeg * pClone = new CSphTokenizer_UTF8MMSeg ();
	pClone->CloneBase ( this, bEscaped );
	pClone->m_dictpath = m_dictpath;
	return pClone;
}

const BYTE* CSphTokenizer_UTF8MMSeg::GetThesaurus(BYTE * sBuffer, int iLength )
{
	css::Segmenter* seg = d_->GetSegmenter(m_dictpath.cstr());
	if(seg)
		return (const BYTE*)seg->thesaurus((const char*)sBuffer, iLength);
	return NULL;
}

#endif

